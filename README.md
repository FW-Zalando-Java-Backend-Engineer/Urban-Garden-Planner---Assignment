# 🌿 Urban Garden Planner - Assignment

Welcome to your MongoDB + Spring Boot assignment!  

We will build a **RESTful API** to help **urban gardeners** plan and track their plantings.  
Instead of a shop or library example, this will let users store and retrieve information about the **plants they want to grow**, their **care instructions**, and **seasonal notes**.

---

## 🎯 Goal of the Assignment

✅ Learn how to integrate **Spring Boot** with **MongoDB Atlas**  
✅ Understand **RESTful API** design  
✅ Practice **data modeling** in MongoDB  
✅ Apply **algorithmic thinking** to CRUD design  
✅ Work with **real-world inspired domain**  

---

## 🌱 Background Explanation

Urban gardening is growing in popularity. But many gardeners struggle to track:

- Which plants they want to grow
- Planting dates
- Care instructions
- Notes about growth and harvest

Your task: **Design a REST API** that lets users store and manage their personal urban garden plan.

---

## 📌 Why This Assignment?

👉 It uses **real-world thinking** (planning, storing structured info)  
👉 It requires **NoSQL design** (flexible, schema-less)  
👉 It aligns with **REST principles** (resources, methods)  
👉 It will teach you **step-by-step** how to:

- Model data
- Create a Spring Boot project
- Connect to MongoDB Atlas
- Expose REST endpoints

---

## 💡 Theory and Concepts

Before you start coding, make sure you understand:

### 🌿 What is MongoDB?

- **NoSQL** database storing JSON-like documents.
- Flexible schema, ideal for evolving data.
- Good for storing structured but non-relational info (like plant records with notes).

---

### 🌿 What is MongoDB Atlas?

- MongoDB's **cloud service**.
- Free tier available for learning.
- Gives you a **connection string** for easy integration.

---

### 🌿 What is Spring Boot?

- Java framework for building web applications.
- Provides auto-configuration and starter templates.
- Integrates seamlessly with MongoDB via **Spring Data MongoDB**.

---

### 🌿 What is REST?

- Architectural style for APIs.
- Treats **everything as a resource**.
- Uses standard HTTP methods:

| Method | Action             |
| ------ | ------------------ |
| GET    | Retrieve data      |
| POST   | Create new data    |
| PUT    | Update existing    |
| DELETE | Remove data        |

---

## 📌 How Does This All Fit Together?

Your Spring Boot app will:

✅ Expose a **REST API** for urban gardeners.  
✅ Store data in **MongoDB Atlas**.  
✅ Use **Spring Data MongoDB** to map Java objects to MongoDB documents.  
✅ Let users **add**, **view**, **update**, and **delete** plant plans.

---

## 🛠️ Example Use Case

- User plans to grow tomatoes.
- User adds **Tomato** with notes on planting date, watering, sunlight needs.
- Later, user updates the notes or deletes the plan if not needed.

---

## 📌 Data Model

We’ll design a **PlantPlan** document:

| Field          | Type    | Description                         |
| --------------- | ------- | ----------------------------------- |
| id              | String  | Unique identifier (MongoDB _id)     |
| name            | String  | Name of the plant                   |
| plantingSeason  | String  | Recommended planting season         |
| sunlightNeeds   | String  | Sunlight requirements (e.g. Full Sun)|
| wateringFreq    | String  | How often to water                  |
| notes           | String  | Additional notes                    |

---

## 🧭 Assignment Steps

Below is your **algorithmic plan**, theory included for each step.

---

### ✅ Step 1: Set Up MongoDB Atlas

**Theory:**  
MongoDB Atlas is a cloud database service. Instead of installing MongoDB locally, we get a secure, globally available database.

**Algorithmic Steps:**  
1. Sign up at [MongoDB Atlas](https://www.mongodb.com/cloud/atlas).  
2. Create a **Free Tier Cluster**.  
3. Add a **Database User** with username/password.  
4. Set **Network Access** to allow your IP or 0.0.0.0/0.  
5. Copy your **connection string**.

> 💬 **WHY?**  
> This connection string will let your Spring Boot app authenticate and connect to the cloud database.

---

### ✅ Step 2: Initialize Spring Boot Project

**Theory:**  
Spring Boot provides everything you need to create a REST API quickly. We’ll use Maven to manage dependencies.

**Algorithmic Steps:**  
1. Create a new Maven project.  
2. Add dependencies:  
   - Spring Web  
   - Spring Data MongoDB  
   - Lombok (optional, for cleaner code)

**application.properties:**
```

spring.data.mongodb.uri=mongodb+srv://<username>:<password>@cluster0.mongodb.net/urban\_garden?retryWrites=true\&w=majority
spring.data.mongodb.database=urban\_garden

```

> 💬 **WHY?**  
> The URI tells Spring how to connect to Atlas. The database name is where our plant plans will be stored.

---

### ✅ Step 3: Define the Data Model

**Theory:**  
MongoDB stores documents. In Java, we’ll map them to **POJOs** using Spring Data’s `@Document`.

**Algorithmic Steps:**
- Create a **PlantPlan** class.
- Annotate with `@Document`.
- Include fields with getters/setters.

**Example:**
```java
/**
 * Represents a gardening plan for a single plant.
 */
@Document(collection = "plantplans")
public class PlantPlan {

    @Id
    private String id;
    private String name;
    private String plantingSeason;
    private String sunlightNeeds;
    private String wateringFreq;
    private String notes;

    // Getters and setters...
}
```

> 💬 **WHY?**
> This maps the Java object to a MongoDB document in the *plantplans* collection.

---

### ✅ Step 4: Create Repository

**Theory:**
Spring Data MongoDB can auto-generate CRUD methods for us.

**Algorithmic Steps:**

* Extend `MongoRepository`.

**Example:**

```java
/**
 * Repository for PlantPlan entities.
 */
public interface PlantPlanRepository extends MongoRepository<PlantPlan, String> {
}
```

> 💬 **WHY?**
> No need to write SQL-like queries. Spring Data handles it.

---

### ✅ Step 5: Build Service Layer

**Theory:**
Business logic lives here. Keeps controllers thin.

**Algorithmic Steps:**

* Autowire the repository.
* Add methods for add, getAll, getById, update, delete.

**Example:**

```java
/**
 * Service for managing plant plans.
 */
@Service
public class PlantPlanService {
    private final PlantPlanRepository repository;

    @Autowired
    public PlantPlanService(PlantPlanRepository repository) {
        this.repository = repository;
    }

    public PlantPlan addPlan(PlantPlan plan) {
        return repository.save(plan);
    }

    public List<PlantPlan> getAllPlans() {
        return repository.findAll();
    }

    public Optional<PlantPlan> getPlanById(String id) {
        return repository.findById(id);
    }

    public PlantPlan updatePlan(String id, PlantPlan updated) {
        updated.setId(id);
        return repository.save(updated);
    }

    public void deletePlan(String id) {
        repository.deleteById(id);
    }
}
```

> 💬 **WHY?**
> Centralizes business rules, making code testable and maintainable.

---

### ✅ Step 6: Expose REST Controller

**Theory:**
The controller is the entry point for HTTP requests.

**Algorithmic Steps:**

* Autowire the service.
* Define endpoints:

| Method | Endpoint        | Action          |
| ------ | --------------- | --------------- |
| POST   | /api/plans      | Add new plan    |
| GET    | /api/plans      | Get all plans   |
| GET    | /api/plans/{id} | Get plan by ID  |
| PUT    | /api/plans/{id} | Update existing |
| DELETE | /api/plans/{id} | Delete plan     |

**Example:**

```java
/**
 * REST Controller for Plant Plans.
 */
@RestController
@RequestMapping("/api/plans")
public class PlantPlanController {

    private final PlantPlanService service;

    @Autowired
    public PlantPlanController(PlantPlanService service) {
        this.service = service;
    }

    @PostMapping
    public PlantPlan addPlan(@RequestBody PlantPlan plan) {
        return service.addPlan(plan);
    }

    @GetMapping
    public List<PlantPlan> getAllPlans() {
        return service.getAllPlans();
    }

    @GetMapping("/{id}")
    public ResponseEntity<PlantPlan> getPlanById(@PathVariable String id) {
        return service.getPlanById(id)
                .map(ResponseEntity::ok)
                .orElse(ResponseEntity.notFound().build());
    }

    @PutMapping("/{id}")
    public PlantPlan updatePlan(@PathVariable String id, @RequestBody PlantPlan plan) {
        return service.updatePlan(id, plan);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deletePlan(@PathVariable String id) {
        service.deletePlan(id);
        return ResponseEntity.noContent().build();
    }
}
```

> 💬 **WHY?**
> These REST endpoints follow standard CRUD design, making it easy for frontend apps or other services to consume.

---

### ✅ Step 7: Test with Postman or cURL

**Algorithmic Steps:**

* Add a plan with POST.
* Retrieve all with GET.
* Update with PUT.
* Delete with DELETE.

> 💬 **WHY?**
> Verifies your API is working end-to-end.

---

## 📌 💡 Discussion / Algorithmic Thinking Prompts

* How does MongoDB's flexibility help with evolving data (e.g. adding new fields)?
* Why split code into **model**, **repository**, **service**, **controller**?
* How would you handle validation?
* How would you model *companion planting* or *pest management* if you wanted to extend the app?

---

## ✅ Final Submission Checklist

✔️ MongoDB Atlas cluster created
✔️ Connection string in `application.properties`
✔️ Spring Boot project with:

* Model
* Repository
* Service
* Controller

✔️ REST endpoints working
✔️ README in your repo explaining your setup

---

## 🌟 Remember

**"Don't just code. Think about the why."**

* How does MongoDB help your design?
* Why is REST a good pattern here?
* How does Spring Data simplify CRUD?

---

Happy gardening! 🌿

---
