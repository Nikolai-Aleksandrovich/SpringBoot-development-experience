# Model, ModelMap, and ModelAndView in Spring MVC

## Model

the model can supply attributes used for rendering views.

To provide a view with usable data, we simply add this data to its *Model* object. Additionally, maps with attributes can be merged with *Model* instances:

```
@GetMapping("/showViewPage")
public String passParametersWithModel(Model model) {
    Map<String, String> map = new HashMap<>();
    map.put("spring", "mvc");
    model.addAttribute("message", "Baeldung");
    model.mergeAttributes(map);
    return "viewPage";
}
```

## ModelMap

Just like the *Model* interface above, *ModelMap* is also used to pass values to render a view.

The advantage of *ModelMap* is it gives us the ability to pass a collection of values and treat these values as if they were within a *Map*:

```
@GetMapping("/printViewPage")
public String passParametersWithModelMap(ModelMap map) {
    map.addAttribute("welcomeMessage", "welcome");
    map.addAttribute("message", "Baeldung");
    return "viewPage";
}
```

## ModelAndView

The final interface to pass values to a view is the *ModelAndView*.

This interface allows us to pass all the information required by Spring MVC in one return:

```
@GetMapping("/goToViewPage")
public ModelAndView passParametersWithModelAndView() {
    ModelAndView modelAndView = new ModelAndView("viewPage");
    modelAndView.addObject("message", "Baeldung");
    return modelAndView;
}
```

## 