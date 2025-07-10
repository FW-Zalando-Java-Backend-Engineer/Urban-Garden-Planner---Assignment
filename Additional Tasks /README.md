# 🌿 Urban Garden Planner - Additional Tasks

Welcome back, gardeners! 🌱  

This time, you're **enhancing** your Urban Garden Planner REST API.  

Below you'll find **new tasks**, **starter code**, and **full explanations** so you can continue developing your skills.

---

## ✅ 🎯 Goals for This Assignment Extension

- Practice **advanced REST design**  
- Learn **custom queries** in Spring Data MongoDB  
- Add **update and delete** endpoints  
- Add **validation** for data integrity  
- Add **calculation endpoints** for real-world value

---

## 📌 🌿 Background Reminder

Your Urban Garden Planner is an app to help people plan and track **what they want to plant**, **how to care for it**, and **seasonal notes**.

Each **PlantPlan** record contains:

| Field          | Type    | Description                         |
| --------------- | ------- | ----------------------------------- |
| id              | String  | Unique identifier                   |
| name            | String  | Name of the plant                   |
| plantingSeason  | String  | Recommended planting season         |
| sunlightNeeds   | String  | Sunlight requirements               |
| wateringFreq    | String  | How often to water                  |
| notes           | String  | Additional notes                    |

---

## ✅ 🔥 New Tasks

### Task 1️⃣ Add Update Endpoint (PUT)
✅ Allow users to **update** existing plant plans by ID.  
✅ Use HTTP **PUT**.  
✅ Validate input.

---

### Task 2️⃣ Add Delete Endpoint (DELETE)
✅ Let users **delete** a plant plan by ID.  
✅ Use HTTP **DELETE**.  
✅ Return appropriate HTTP status codes.

---

### Task 3️⃣ Add Validation
✅ Use `@Valid` and javax validation annotations.  
✅ Enforce:  
- `@NotBlank` for **name**, **plantingSeason**, **sunlightNeeds**  
- Optional notes allowed  

✅ Why?  
👉 Prevent incomplete or invalid data.

---

### Task 4️⃣ Add 5 Custom Queries
✅ Help gardeners search and plan better:

⭐ 1. Find plans by **plantingSeason**  
⭐ 2. Find plans by **sunlightNeeds**  
⭐ 3. Find plans with **wateringFreq** containing a keyword  
⭐ 4. Count plans by **plantingSeason**  
⭐ 5. Search by **name** (case-insensitive, partial match)

---

### Task 5️⃣ Add Calculation Endpoint
✅ Add a **summary** endpoint:  
✅ Return **count of all plans** in the system.  
✅ Why?  
👉 Helps users know their overall planting plan size.

---

---

## ✅ 💡 Starter Code Snippets

Use these in your project!  

---

### 🌱 PlantPlan Model

```java
import jakarta.validation.constraints.NotBlank;
import org.springframework.data.annotation.Id;
import org.springframework.data.mongodb.core.mapping.Document;

/**
 * Represents a plan for planting a specific plant.
 */
@Document(collection = "plantplans")
public class PlantPlan {

    @Id
    private String id;

    @NotBlank(message = "Name is required")
    private String name;

    @NotBlank(message = "Planting season is required")
    private String plantingSeason;

    @NotBlank(message = "Sunlight needs is required")
    private String sunlightNeeds;

    private String wateringFreq;
    private String notes;

    // Constructors, Getters, Setters
}
````

✅ **Why?**

> Enforces essential fields to ensure meaningful plans.

---

### 🌱 PlantPlanRepository

```java
import org.springframework.data.mongodb.repository.MongoRepository;
import org.springframework.data.mongodb.repository.Query;
import java.util.List;

public interface PlantPlanRepository extends MongoRepository<PlantPlan, String> {

    // 1. Find by plantingSeason
    List<PlantPlan> findByPlantingSeason(String plantingSeason);

    // 2. Find by sunlightNeeds
    List<PlantPlan> findBySunlightNeeds(String sunlightNeeds);

    // 3. Search wateringFreq containing keyword
    List<PlantPlan> findByWateringFreqContainingIgnoreCase(String keyword);

    // 4. Count by plantingSeason
    long countByPlantingSeason(String plantingSeason);

    // 5. Name search (partial, case-insensitive)
    List<PlantPlan> findByNameContainingIgnoreCase(String keyword);
}
```

✅ **Why?**

> Real gardeners need flexible search to plan by season, sun, watering, etc.

---

### 🌱 PlantPlanService

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;
import java.util.List;
import java.util.Optional;

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

    // Custom queries
    public List<PlantPlan> findByPlantingSeason(String season) {
        return repository.findByPlantingSeason(season);
    }

    public List<PlantPlan> findBySunlightNeeds(String sunlight) {
        return repository.findBySunlightNeeds(sunlight);
    }

    public List<PlantPlan> searchByWateringFreq(String keyword) {
        return repository.findByWateringFreqContainingIgnoreCase(keyword);
    }

    public long countByPlantingSeason(String season) {
        return repository.countByPlantingSeason(season);
    }

    public List<PlantPlan> searchByName(String keyword) {
        return repository.findByNameContainingIgnoreCase(keyword);
    }

    public long getTotalPlansCount() {
        return repository.count();
    }
}
```

---

### 🌱 PlantPlanController

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;
import jakarta.validation.Valid;
import java.util.List;

@RestController
@RequestMapping("/api/plans")
public class PlantPlanController {

    private final PlantPlanService service;

    @Autowired
    public PlantPlanController(PlantPlanService service) {
        this.service = service;
    }

    @PostMapping
    public PlantPlan addPlan(@Valid @RequestBody PlantPlan plan) {
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
    public PlantPlan updatePlan(@PathVariable String id, @Valid @RequestBody PlantPlan updatedPlan) {
        return service.updatePlan(id, updatedPlan);
    }

    @DeleteMapping("/{id}")
    public ResponseEntity<Void> deletePlan(@PathVariable String id) {
        service.deletePlan(id);
        return ResponseEntity.noContent().build();
    }

    // Custom query endpoints
    @GetMapping("/season/{season}")
    public List<PlantPlan> getByPlantingSeason(@PathVariable String season) {
        return service.findByPlantingSeason(season);
    }

    @GetMapping("/sunlight/{sunlight}")
    public List<PlantPlan> getBySunlightNeeds(@PathVariable String sunlight) {
        return service.findBySunlightNeeds(sunlight);
    }

    @GetMapping("/watering/search")
    public List<PlantPlan> searchByWateringFreq(@RequestParam String keyword) {
        return service.searchByWateringFreq(keyword);
    }

    @GetMapping("/count/season/{season}")
    public long countByPlantingSeason(@PathVariable String season) {
        return service.countByPlantingSeason(season);
    }

    @GetMapping("/search")
    public List<PlantPlan> searchByName(@RequestParam String keyword) {
        return service.searchByName(keyword);
    }

    // Calculation endpoint
    @GetMapping("/count/all")
    public long getTotalPlansCount() {
        return service.getTotalPlansCount();
    }
}
```

✅ **Why?**

> Exposes clear RESTful endpoints for CRUD, custom queries, and summary stats.

---

## ✅ 🌟 Testing Examples

✅ Add Plan:

```
POST /api/plans
{
  "name": "Tomato",
  "plantingSeason": "Spring",
  "sunlightNeeds": "Full Sun",
  "wateringFreq": "Twice a week",
  "notes": "Prefers warm soil"
}
```

✅ Update Plan:

```
PUT /api/plans/{id}
{
  "name": "Tomato Updated",
  "plantingSeason": "Spring",
  "sunlightNeeds": "Full Sun",
  "wateringFreq": "Three times a week",
  "notes": "Mulch recommended"
}
```

✅ Custom Queries:

```
GET /api/plans/season/Spring
GET /api/plans/sunlight/Full Sun
GET /api/plans/watering/search?keyword=week
GET /api/plans/count/season/Spring
GET /api/plans/search?keyword=tomato
GET /api/plans/count/all
```

---

## ✅ 📌 Summary

By completing these tasks you will:

⭐ Master CRUD with validation
⭐ Expose meaningful REST endpoints
⭐ Write custom queries for real-world questions
⭐ Provide useful calculation endpoints for users

---

## ✅ 💥 Challenge Extras (Optional)

* Add pagination and sorting
* Add error handling (e.g. @ControllerAdvice)
* Secure the API with basic auth
* Deploy to Heroku or another cloud

---

### 🌿 Happy coding, gardeners! 🌿

---
