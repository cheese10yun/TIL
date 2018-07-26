[Jackson Annotation Examples](http://www.baeldung.com/jackson-annotations) 예제를 적용전, 적용후로 나누어서 정리 해봤습니다.

[GitHub](https://github.com/cheese10yun/blog-sample/tree/master/jackson)에 전체 코드는 공개되어 있어서 테스트코드도 참고하시면 좋을거 같습니다.


<!-- TOC -->

- [2. Jackson Serialization Annotations](#2-jackson-serialization-annotations)
    - [@JsonAnyGetter](#jsonanygetter)
    - [@JsonGetter getter](#jsongetter-getter)
    - [@JsonPropertyOrder](#jsonpropertyorder)
    - [@JsonRawValue Jackson](#jsonrawvalue-jackson)
    - [@JsonValue](#jsonvalue)
    - [@JsonRootName](#jsonrootname)
- [3. Jackson Deserialization Annotations](#3-jackson-deserialization-annotations)
    - [@JsonCreator](#jsoncreator)
    - [@JacksonInject](#jacksoninject)
    - [@JsonAnySetter](#jsonanysetter)
    - [@JsonSetter](#jsonsetter)
- [4. Jackson Property Inclusion Annotations](#4-jackson-property-inclusion-annotations)
    - [@JsonIgnoreProperties](#jsonignoreproperties)
    - [@JsonIgnore](#jsonignore)
    - [@JsonIgnoreType](#jsonignoretype)
    - [@JsonInclude](#jsoninclude)
    - [@JsonAutoDetect](#jsonautodetect)
- [5. Jackson Polymorphic Type Handling Annotations](#5-jackson-polymorphic-type-handling-annotations)
- [6.Jackson General Annotations](#6jackson-general-annotations)
    - [@JsonFormat](#jsonformat)
    - [@JsonUnwrapped](#jsonunwrapped)
    - [@JsonView](#jsonview)
    - [@JsonManagedReference, @JsonBackReference](#jsonmanagedreference-jsonbackreference)
    - [@JsonFilter](#jsonfilter)
- [Custom Jackson Annotation](#custom-jackson-annotation)
- [Disable Jackson Annotation](#disable-jackson-annotation)

<!-- /TOC -->

# 2. Jackson Serialization Annotations

##  @JsonAnyGetter
* 직렬화 할 때 Map의 모든 키 - 값 을 표준 일반 속성으로 가져옵니다
```java
@Getter
@Builder
public static class ExtendableBean {
    public String name;
    private Map<String, String> properties;

    @JsonAnyGetter
    public Map<String, String> getProperties() {
        return properties;
    }
}
```

```json
//적용전
{
  "name": "yun",
  "properties": {
    "key1": "value1",
    "key2": "value2"
  }
}
//적용후
{
  "name": "yun",
  "key1": "value1",
  "key2": "value2"
}
```

## @JsonGetter getter
* 이름 기반으로 키값이 정해지는것을 어노테이션을 제어
```java
@Builder
public static class MyBean {
    public int id;
    private String name;

    @JsonGetter("name")
    public String getTheName() {
        return name;
    }
}
```
```json
//적용전
{
  "id": 1,
  "theName": "yun"
}
//적용후
{
  "id": 1,
  "name": "yun"
}
```

##  @JsonPropertyOrder
* 직렬화 순서를 제어
```java
@JsonPropertyOrder({"name", "id"})
@Builder
public static class PropertyOrder {
    private long id;
    private String name;
}
```

```json
//적용전
{
  "id": 1,
  "name": "name"
}
//적용후
{
  "name": "name",
  "id": 1
}
```

## @JsonRawValue Jackson
* 속성을 그대로 직렬화하여 JSON으로 변경
```java
@Builder
    public static class RawBean {
        public String name;

        @JsonRawValue
        public String json;
    }
```
```json
//적용전
{
  "name": "yun",
  "json": "{\n  \"attr\":false\n}"
}
//적용후
{
  "name": "yun",
  "json": {
    "attr": false
  }
}
```

## @JsonValue
* getName  @JsonValue 해당 멤버필드가 이름을 통해 직렬화 시킴
```java
public enum TypeEnumWithValue {
    TYPE1(1, "Type A"),
    TYPE2(2, "Type 2");

    private Integer id;
    private String name;

    TypeEnumWithValue(Integer id, String name) {
        this.id = id;
        this.name = name;
    }

    @JsonValue
    public String getName() {
        return name;
    }
}
```
```json
//적용전
"TYPE1"
//적용후
"Type A"
```

## @JsonRootName
* Root 이름 지정
```java
@Builder
@JsonRootName(value = "user")
public static class UserWithRoot {
    public int id;
    public String name;
}

//objectMapper.enable(SerializationFeature.WRAP_ROOT_VALUE); 반드시 적용해야함
```
```json
//적용전
{
  "id": 1,
  "name": "yun"
}
//적용후
{
  "user": {
    "id": 1,
    "name": "yun"
  }
}
```

# 3. Jackson Deserialization Annotations

## @JsonCreator
* JSON key 와 멤버 필드의 이름이 일치하지 않을 경우 사용합니다.
```json
{
  "id":1,
  "theName":"My bean"
}
```

```java
public static class BeanWithCreator {
    public int id;
    public String name;

    @JsonCreator
    public BeanWithCreator(
            @JsonProperty("id") int id,
            @JsonProperty("theName") String name
    ) {
        this.id = id;
        this.name = name;
    }
}
```

## @JacksonInject
* JSON 데이터가 아닌 값을 주입하는데 사용됩니다.
```json
{
  "name": "My bean"
}
```

```java
public static class BeanWithInject {
    @JacksonInject
    public int id;

    public String name;
}
```

## @JsonAnySetter
* Map을 이용해서 유연성있게 Deserialization 합니다.
```json
{
  "name": "My bean",
  "attr2": "val2",
  "attr1": "val1"
}
```

```java
public static class ExtendableBean {
    public String name;
    private Map<String, String> properties = new HashMap<>();


    @JsonAnySetter
    public void setProperties(String key, String value) {
        properties.put(key, value);
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }


    public Map<String, String> getProperties() {
        return properties;
    }
}
```

## @JsonSetter
* 객체와 맴버필드와 일치하지 않을 경우 유용하게 사용할 수 있습니다.

```json
{
  "id": 1,
  "name": "My bean"
}
```
```java
public static class MyBean {
    public int id;
    private String name;

    @JsonSetter("name")
    public void setTheName(String name) {
        this.name = name;
    }

    public String getTheName() {
        return this.name;
    }
}
```

# 4. Jackson Property Inclusion Annotations

## @JsonIgnoreProperties

* 무시할 속성이나 속성 목록을 표시하는 데 사용됩니다
```java
@JsonIgnoreProperties({"id"})
public static class BeanWithIgnore {
    public int id;
    public String name;
}
```

```json
{
  "name": "yun"
}
```

## @JsonIgnore

* 필드 레벨에서 무시 될 수있는 속성을 표시하는 데 사용됩니다.
```java
public static class BeanWithIgnore {
    @JsonIgnore
    public int id;
    public String name;
}
```

```json
{
  "name": "yun"
}
```

## @JsonIgnoreType
* 주석이 달린 형식의 모든 속성을 무시하도록 지정하는 데 사용됩니다 
```java
public static class User {
public int id;
public Name name;

    @JsonIgnoreType
    public static class Name {
        public String firstName;
        public String lastName;
    }
}
```
```json
{
  "id": 1
}
```

## @JsonInclude
* 어노테이션 속성을 제외 하는 데 사용 됩니다 

```java
@JsonInclude(JsonInclude.Include.NON_NULL)
@AllArgsConstructor
public static class MyBean {
    public int id;
    public String name;
}
```
```json
//NON_NULL 사용시 name이 null인 경우에 제외 됩니다.
{
  "id": 1
}
```

## @JsonAutoDetect

```java
@JsonAutoDetect(fieldVisibility = JsonAutoDetect.Visibility.ANY)
public static class PrivateBean {
    private int id;
    private String name;
}
```

```json
// Visibility.ANY 경우 표시
{
  "id": 1,
  "name": "yun"
}
```

# 5. Jackson Polymorphic Type Handling Annotations

* Polymorphic을 통한 직렬화, 비직렬화

```java
@NoArgsConstructor(access = AccessLevel.PRIVATE)
public static class Zoo {
    public Animal animal;

    public Zoo(Animal animal) {
        this.animal = animal;
    }

    @JsonTypeInfo(
            use = JsonTypeInfo.Id.NAME,
            include = JsonTypeInfo.As.PROPERTY,
            property = "type")
    @JsonSubTypes({
            @JsonSubTypes.Type(value = Dog.class, name = "dog"),
            @JsonSubTypes.Type(value = Cat.class, name = "cat")
    })
    @Getter
    @NoArgsConstructor(access = AccessLevel.PRIVATE)
    public static class Animal {
        private String name;

        public Animal(String name) {
            this.name = name;
        }
    }

    @JsonTypeName("dog")
    @Getter
    @NoArgsConstructor(access = AccessLevel.PRIVATE)
    public static class Dog extends Animal {
        public double barkVolume;

        public Dog(String name) {
            super(name);
        }
    }

    @JsonTypeName("cat")
    @Getter
    @NoArgsConstructor(access = AccessLevel.PRIVATE)
    public static class Cat extends Animal {
        boolean likesCream;
        public int lives;

        public Cat(String name) {
            super(name);
        }
    }

}
```
```json
{
    "animal": {
        "type": "dog",
        "name": "lacy",
        "barkVolume": 0
    }
}

{
    "animal":{
        "name":"lacy",
        "type":"cat"
    }
}
```

# 6.Jackson General Annotations

## @JsonFormat

* 날짜 / 시간 값을 직렬화 할 때 포멧팅을 지정합니다
```java
public static class Event {
    public String name;

    @JsonFormat(
            shape = JsonFormat.Shape.STRING,
            pattern = "dd-MM-yyyy hh:mm:ss")
    public Date eventDate;

    public Event(String name, Date date) {
        this.name = name;
        this.eventDate = date;
    }
}
```
```json
//적용전
{
  "name":"party",
  "eventDate":1419042600000
}
//적용후
{
  "name": "party",
  "eventDate": "20-12-2014 02:30:00"
}
```

## @JsonUnwrapped
* 직렬화, 비 직렬화 될 때 언 래핑 / 병합되어야 하는 값 을 정의하는 데 사용됩니다
```java
public static class UnwrappedUser {
    public int id;

    @JsonUnwrapped
    public Name name;

    public UnwrappedUser(int id, Name name) {
        this.id = id;
        this.name = name;
    }

    public static class Name {
        public String firstName;
        public String lastName;

        public Name(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
    }
}
```

```json
//적용전
{
  "id": 1,
  "firstName": "John",
  "lastName": "Doe"
}
//적용후
{
  "id":1,
  "name":{
    "firstName":"John",
    "lastName":"Doe"
  }
}
```

## @JsonView
* 속성이 serialization / deserialization에 포함될 View 를 나타내는 데 사용됩니다.
```java
public static class Views {
    public static class Public {
    }

    public static class Internal extends Public {
    }
}

public static class Item {
    @JsonView(Views.Public.class)
    public int id;

    @JsonView(Views.Public.class)
    public String itemName;

    @JsonView(Views.Internal.class)
    public String ownerName;

    public Item(int id, String itemName, String ownerName) {
        this.id = id;
        this.itemName = itemName;
        this.ownerName = ownerName;
    }
}
```
```json
//적용전
{
  "id":2,
  "itemName":"book",
  "ownerName":"John"
}
//적용후
{
  "id":2,
  "itemName":"book"
}
```

## @JsonManagedReference, @JsonBackReference
* 객체의  상위 / 하위 관계를 처리 명시하고 무한 순함참조에러를 해결합니다.
```java
public static class ItemWithRef {
    public int id;
    public String itemName;

    @JsonManagedReference
    public UserWithRef owner;

    public ItemWithRef(int id, String itemName, UserWithRef owner) {
        this.id = id;
        this.itemName = itemName;
        this.owner = owner;
    }
}

public static class UserWithRef {
    public int id;
    public String name;

    @JsonBackReference
    public List<ItemWithRef> itemWithRefs = new ArrayList<>();

    public UserWithRef(int id, String name) {
        this.id = id;
        this.name = name;
    }

    public void addItem(ItemWithRef item) {
        itemWithRefs.add(item);
    }
}
```
```json
//적용전
// Infinite recursion (StackOverflowError)... 무한 순함참조 에러
//적용후
{
  "id":2,
  "itemName":"book",
  "owner":{
    "id":1,
    "name":"John"
  }
}
```

## @JsonFilter
* 직렬화시에 사용되는 필터를 지정합니다.
```java
@JsonFilter("myFilter")
public static class BeanWithFilter {
    public int id;
    public String name;

    public BeanWithFilter(int id, String name) {
        this.id= id;
        this.name = name;
    }
}
```
```json
//적용전
{
  "id":1,
  "name":"My bean"
}
//적용후
{
  "name":"My bean"
}
```

# Custom Jackson Annotation

* Annotation 직접 정리 할 수 있습니다.

```java
@CustomAnnotation
public static class BeanWithCustomAnnotation {
    public int id;
    public String name;
    public Date dateCreated;

    public BeanWithCustomAnnotation(int id, String name, Date dateCreated) {
        this.id = id;
        this.name = name;
        this.dateCreated = dateCreated;
    }
}

@Retention(RetentionPolicy.RUNTIME)
@JacksonAnnotationsInside
@JsonInclude(JsonInclude.Include.NON_NULL)
@JsonPropertyOrder({"name", "id", "dateCreated"})
@interface CustomAnnotation {
}
```
```json
// 적용전
{
  "id": 1,
  "name": "My bean",
  "dateCreated": null
}
// 적용후, property order 변경, null 값 비 직렬화
{
  "name": "My bean",
  "id": 1
}
```

# Disable Jackson Annotation

* 모든 Jackson annotation 비활성화 하는 방법

 ```java
@JsonInclude(JsonInclude.Include.NON_NULL)
@JsonPropertyOrder({"name", "id"})
 public static class MyBean {
     public int id;
     public String name;

     public MyBean(int id, String name) {
         this.id = id;
         this.name = name;
     }
 }
mapper.disable(MapperFeature.USE_ANNOTATIONS); // 모든 Jackson annotation 비활성화
 ```
 ```json
 // MapperFeature.USE_ANNOTATIONS 적용전
 {
     "id":1,
     "name":null
 }

 // MapperFeature.USE_ANNOTATIONS 적용후
 {
   "id": 1
 }
 ```