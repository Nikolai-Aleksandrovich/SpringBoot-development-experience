# Spring Data Redis

Redis is driven by a keystore-based data structure to persist data and can be used as a database, cache, message broker, etc.（popular in-memory data structure store.）

## **Maven Dependencies**

```
<dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>2.3.3.RELEASE</version>
 </dependency>

<dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>3.3.0</version>
    <type>jar</type>
</dependency>
```

## Spring Boot starter for Redis

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
    <version>2.3.3.RELEASE</version>
</dependency>
```

## **The Redis Configuration**

There is a number of Redis client implementations available for Java. we'll use [Jedis](https://github.com/xetorthio/jedis) – a simple and powerful Redis client implementation**.

### **Java Configuration**

First, using the Jedis client, we're defining a *connectionFactory*.

Then, we defined a *RedisTemplate* using the *jedisConnectionFactory.* This can be used for querying data with a custom repository.

```
@Bean
JedisConnectionFactory jedisConnectionFactory() {
    return new JedisConnectionFactory();
}

@Bean
public RedisTemplate<String, Object> redisTemplate() {
    RedisTemplate<String, Object> template = new RedisTemplate<>();
    template.setConnectionFactory(jedisConnectionFactory());
    return template;
}
```

### **Custom Connection Properties**

 if we need to configure the connection details, we can always modify the *jedisConnectionFactory* configuration as follows:

```
@Bean
JedisConnectionFactory jedisConnectionFactory() {
    JedisConnectionFactory jedisConFactory
      = new JedisConnectionFactory();
    jedisConFactory.setHostName("localhost");
    jedisConFactory.setPort(6379);
    return jedisConFactory;
}
```

## **Redis Repository**

```
@RedisHash("Student")
public class Student implements Serializable {
  
    public enum Gender { 
        MALE, FEMALE
    }

    private String id;
    private String name;
    private Gender gender;
    private int grade;
    // ...
}
```

### **The Spring Data Repository**

@Repository public interface **StudentRepository** extends **CrudRepository**<**Student**, **String**> {}

## **Data Access Using StudentRepository**

**By extending \*CrudRepository\* in \*StudentRepository\*, we automatically get a complete set of persistence methods that perform CRUD functionality.**

### **Saving a New Student Object**

```
Student student = new Student(
  "Eng2015001", "John Doe", Student.Gender.MALE, 1);
studentRepository.save(student);
```

### **Retrieving an Existing Student Object**

verify the correct insertion of the student in the previous section by fetching the student data:

```
Student retrievedStudent = 
  studentRepository.findById("Eng2015001").get();
```

### **Updating an Existing Student Object**

change the name of the student retrieved above and save it again

```
retrievedStudent.setName("Richard Watson");
studentRepository.save(student);
```

### **Deleting an Existing Student Data**

```
studentRepository.deleteById(student.getId());
```

### **Find All Student Data**

 insert a few student objects:

```
Student engStudent = new Student(
  "Eng2015001", "John Doe", Student.Gender.MALE, 1);
Student medStudent = new Student(
  "Med2015001", "Gareth Houston", Student.Gender.MALE, 2);
studentRepository.save(engStudent);
studentRepository.save(medStudent);
```

We can also achieve this by inserting a collection. For that, there is a different method – *saveAll()* – which accepts a single *Iterable* object containing multiple student objects that we want to persist.

then,find all:

```
List<Student> students = new ArrayList<>();
studentRepository.findAll().forEach(students::add);
```

Then we can quickly check the size of the *students* list or verify for a greater granularity by checking the properties of each object.