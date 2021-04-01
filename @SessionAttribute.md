# Session Attributes in Spring MVC

When developing web applications, we often need to refer to the same attributes in several views.

**examine 2 different strategies for working with a session attribute**:

- **Using a scoped proxy**
- **Using the @SessionAttributes annotation**

## **Example Use Case**

Simple Pojo

```
@Data
public class TodoItem{
	private String description;
    private LocalDateTime createDate;
}
```

Our *TodoList* class extends *ArrayDeque* to give us convenient access to the most recently added item via the *peekLast* method.

```
public class TodoList extends ArrayDeque<TodoItem>{

}
```

three controller for different function:

* ***@GetMapping(“/form”)*** – This method will be responsible for initializing the form and rendering the form view. The method will prepopulate the form with the most recently added *TodoItem* if the *TodoList* is not empty.
* ***@PostMapping(“/form”)\*** – This method will be responsible for adding the submitted *TodoItem* to the *TodoList* and redirecting to the list URL.
* ***@GetMapping(“/todos.html”) –\*** This method will simply add the *TodoList* to the *Model* for display and render the list view.

## **Using a Scoped Proxy**

First, we define our bean within a *@Configuration* class:

```java
@Bean
@Scope(
  value = WebApplicationContext.SCOPE_SESSION, 
  proxyMode = ScopedProxyMode.TARGET_CLASS)
public TodoList todos() {
    return new TodoList();
}
```

Next, we declare the bean as a dependency for the *@Controller* and inject it just as we would any other dependency:

```java
@Controller
@RequestMapping("/scopedproxy")
public class TodoControllerWithScopedProxy {

    private TodoList todos;

    // constructor and request mappings
}
```

Finally, using the bean in a request simply involves calling its methods:

```java
@GetMapping("/form")
public String showForm(Model model) {
    if (!todos.isEmpty()) {
        model.addAttribute("todo", todos.peekLast());
    } else {
        model.addAttribute("todo", new TodoItem());
    }
    return "scopedproxyform";
}
```

### **Unit Testing**

In order to test our implementation using the scoped proxy, **we first configure a \*SimpleThreadScope\****.* This will ensure that our unit tests accurately simulate runtime conditions of the code we are testing.

First, we define a *TestConfig* and a *CustomScopeConfigurer*:

```
@Configuration
public class TestConfig {
    @Bean
    public CustomScopeConfigurer customScopeConfigurer() {
        CustomScopeConfigurer configurer = new CustomScopeConfigurer();
        configurer.addScope("session", new SimpleThreadScope());
        return configurer;
    }
}
```

Now we can start by testing that an initial request of the form contains an uninitialized *TodoItem:*

```
@RunWith(SpringRunner.class) 
@SpringBootTest
@AutoConfigureMockMvc
@Import(TestConfig.class) 
public class TodoControllerWithScopedProxyIntegrationTest {

    // ...

    @Test
    public void whenFirstRequest_thenContainsUnintializedTodo() throws Exception {
        MvcResult result = mockMvc.perform(get("/scopedproxy/form"))
          .andExpect(status().isOk())
          .andExpect(model().attributeExists("todo"))
          .andReturn();

        TodoItem item = (TodoItem) result.getModelAndView().getModel().get("todo");
 
        assertTrue(StringUtils.isEmpty(item.getDescription()));
    }
}
```

We can also confirm that our submit issues a redirect and that a subsequent form request is prepopulated with the newly added *TodoItem*:

```
@Test
public void whenSubmit_thenSubsequentFormRequestContainsMostRecentTodo() throws Exception {
    mockMvc.perform(post("/scopedproxy/form")
      .param("description", "newtodo"))
      .andExpect(status().is3xxRedirection())
      .andReturn();

    MvcResult result = mockMvc.perform(get("/scopedproxy/form"))
      .andExpect(status().isOk())
      .andExpect(model().attributeExists("todo"))
      .andReturn();
    TodoItem item = (TodoItem) result.getModelAndView().getModel().get("todo");
 
    assertEquals("newtodo", item.getDescription());
}
```

## discussion

A key feature of using the scoped proxy strategy is that **it has no impact on request mapping method signatures.** This keeps readability on a very high level compared to the *@SessionAttributes* strategy.

It can be helpful to recall that controllers have *singleton* scope by default.

This is the reason why we must use a proxy instead of simply injecting a non-proxied session-scoped bean. **We can't inject a bean with a lesser scope into a bean with greater scope.**

Attempting to do so, in this case, would trigger an exception with a message containing: *Scope ‘session' is not active for the current thread*.

If we're willing to define our controller with session scope, we could avoid specifying a *proxyMode*. This can have disadvantages, especially if the controller is expensive to create because a controller instance would have to be created for each user session.

## **Using the \*@SessionAttributes\* Annotation**

### **Setup**

In this setup, we don't define *TodoList* as a Spring-managed *@Bean*. Instead, we **declare it as a \*@ModelAttribute\* and specify the \*@SessionAttributes\* annotation to scope it to the session for the controller**.

The first time our controller is accessed, Spring will instantiate an instance and place it in the *Model*. Since we also declare the bean in *@SessionAttributes*, Spring will store the instance.

First, we declare our bean by providing a method on the controller and we annotate the method with *@ModelAttribute*:

```
@ModelAttribute("todos")
public TodoList todos() {
    return new TodoList();
}
```

Next, we inform the controller to treat our *TodoList* as session-scoped by using *@SessionAttributes*:

```
@Controller
@RequestMapping("/sessionattributes")
@SessionAttributes("todos")
public class TodoControllerWithSessionAttributes {
    // ... other methods
}
```

Finally, to use the bean within a request, we provide a reference to it in the method signature of a *@RequestMapping*:

```
@GetMapping("/form")
public String showForm(
  Model model,
  @ModelAttribute("todos") TodoList todos) {
 
    if (!todos.isEmpty()) {
        model.addAttribute("todo", todos.peekLast());
    } else {
        model.addAttribute("todo", new TodoItem());
    }
    return "sessionattributesform";
}
```

In the *@PostMapping* method, we inject *RedirectAttributes* and call *addFlashAttribute* before returning our *RedirectView*. This is an important difference in implementation compared to our first example:

```
@PostMapping("/form")
public RedirectView create(
  @ModelAttribute TodoItem todo, 
  @ModelAttribute("todos") TodoList todos, 
  RedirectAttributes attributes) {
    todo.setCreateDate(LocalDateTime.now());
    todos.add(todo);
    attributes.addFlashAttribute("todos", todos);
    return new RedirectView("/sessionattributes/todos.html");
}
```

Spring uses a specialized *RedirectAttributes* implementation of *Model* for redirect scenarios to support the encoding of URL parameters. During a redirect, any attributes stored on the *Model* would normally only be available to the framework if they were included in the URL.

**By using \*addFlashAttribute\* we are telling the framework that we want our \*TodoList\* to survive the redirect** without needing to encode it in the URL.

### Unit Testing

```
@Test
public void whenTodoExists_thenSubsequentFormRequestContainsesMostRecentTodo() throws Exception {
    FlashMap flashMap = mockMvc.perform(post("/sessionattributes/form")
      .param("description", "newtodo"))
      .andExpect(status().is3xxRedirection())
      .andReturn().getFlashMap();

    MvcResult result = mockMvc.perform(get("/sessionattributes/form")
      .sessionAttrs(flashMap))
      .andExpect(status().isOk())
      .andExpect(model().attributeExists("todo"))
      .andReturn();
    TodoItem item = (TodoItem) result.getModelAndView().getModel().get("todo");
 
    assertEquals("newtodo", item.getDescription());
}
```

### **Discussion**

The *@ModelAttribute* and *@SessionAttributes* strategy for storing an attribute in the session is a straightforward solution that **requires no additional context configuration or Spring-managed \*@Bean\*s**.

Unlike our first example, it's necessary to inject *TodoList* in the *@RequestMapping* methods*.*

In addition, we must make use of flash attributes for redirect scenarios.

# Conclusion

any attributes stored in session will only survive for the life of the session.

If we needed to persist attributes between server restarts or session timeouts, we could consider using Spring Session to transparently handle saving the information.