# Integrating an Angular project with Spring Boot

## Step 1: setup your Spring Boot application

When you’re all setup and ready, open the project with your favorite IDE. Your directory structure should look something like this:

![img](https://miro.medium.com/max/536/1*wL9UX6B1vf9LMNjVlRWysw.png)

Let’s setup a restcontroller quickly and get a response back from the web server to make sure everything is working fine:

![img](https://miro.medium.com/max/1299/1*p7Wjyi7Bq6uugSVxQkZHeg.png)

If everything works fine, go back to the class **RestController** class and remove the following code:

```
@RequestMapping("/")
public String greet(){
   return "welcome!";
}
```

## Step 2: setup your Angular application

Start by creating a new directory called **frontend** (under **resources**), and navigate in your terminal to it **src/main/resources/frontend/**

Run the command **ng new angular-app** to create a new angular project within our Spring Boot project. Your project structure should look like this:

![img](https://miro.medium.com/max/437/1*T5w2X-e0JBaqoLv_g1Z1Cg.png)

Now we need to change the configuration in such a way that Angular can build it’s files and provides them for Spring Boot to use. To do that we must edit the **angular.json** file and change the **outputPath** value to generate the build files in **src/main/resources/public/** directory of the project.

![img](https://miro.medium.com/max/774/1*Wh7VadlgmVjVajVyufc-AQ.png)

## Step 3: package your project

From the root of the project, type **mvn package**. Maven will now build the Angular project and place the files in the **public** directory.

## Done:

Now go ahead and run your jar using **java -jar target/name-of-jar.jar** and navigate to [http://localhost:8080,](http://localhost:8080,/) you should see the the default Angular project index.html:

**Optional:**

If you don’t want to keep the Angular project embedded within the Spring Boot project, once you package the Spring Boot project and generated the required files to the **public** directory, go ahead and remove the Angular project from the **resources** directory, and remove the plugin from the **pom** file.

# How to Integrate Angular with Spring Boot RESTful API

Angular web application framework is used by developers to create reactive Single-Page-Applications (SPAs). To give you a background on Single-Page Applications, they’re web applications that load a single HTML page and dynamically update the page in response to user’s interaction.

