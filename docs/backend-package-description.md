# Backend Package Description

## Package Descriptions

| No | Package | Description |
|----|---------|-------------|
| 01 | BitNow-Backend.API | Main API layer containing Controllers and RealTime modules. Handles incoming HTTP requests and manages real-time communication. |
| 02 | BitNow-Backend.BLL | Business Logic Layer responsible for processing business rules, validations, and coordinating workflow between API and DAL. |
| 03 | Services | Implements business logic operations. Receives calls from Controllers and interacts with Repositories through interfaces. |
| 04 | IServices | Interfaces that define the contracts for Service classes. Ensures abstraction, decoupling, and easier testing. |
| 05 | BitNow-Backend.DAL | Data Access Layer that communicates with the database through Models and Repositories. Handles data persistence and queries. |
| 06 | Models | Entity classes representing database tables. Used for data storage and retrieval operations within the DAL. |
| 07 | DTOs | Data Transfer Objects used to move structured data between layers while ensuring consistency and data shaping. |
| 08 | Repositories | Concrete implementations of data access logic. Executes queries and CRUD operations using Models. |
| 09 | IRepositories | Interfaces defining repository contracts. Supports dependency inversion by allowing the BLL to depend on abstractions instead of concrete classes. |
| 10 | Controllers | Entry points of the API. Accept requests, call Services, and return responses to the client. |
| 11 | Helpers | Utility classes providing helper functions for common operations. Contains RoleHelper for role-based access control checks (admin, staff, support) used by Controllers. |
| 12 | Payment | Payment processing module within BLL layer. Handles order management and PayOS payment gateway integration. Implements payment link creation, webhook handling, and payment status management. |


