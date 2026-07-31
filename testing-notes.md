# TaskFlow API Testing Notes

## Environment

* Base URL: `https://test-writechoice.onrender.com`
* Testing tool: Postman
* Date tested: July 31, 2026
* Authentication method: Bearer token
* OpenAPI version: `3.1.0`

> The API key was stored in a Postman environment variable and was not added to this repository.

---

## Test Summary

The live TaskFlow API was tested against the provided OpenAPI specification.

The complete task workflow was successfully tested:

1. Create a project.
2. Create a task inside the project.
3. List the project's tasks.
4. Update the task.
5. Delete the task.

Authentication, validation, empty-state, and resource-not-found scenarios were also tested.

One difference was found between the OpenAPI specification and the behavior of the live API: an empty task update request was accepted even though the specification states that at least one update field must be provided.

---

# Successful Workflow

## 1. Create a Project

* Endpoint: `POST /projects`
* Status received: `201 Created`
* Matches OpenAPI specification: Yes

### Request

```json
{
  "name": "TaskFlow Documentation",
  "description": "Project created while testing the TaskFlow API documentation."
}
```

### Response

```json
{
  "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
  "name": "TaskFlow Documentation",
  "description": "Project created while testing the TaskFlow API documentation.",
  "created_at": "2026-07-31T16:14:28.818Z"
}
```

### Notes

The API generated a unique `project_id`.

This ID was required when creating a task and listing the tasks associated with the project.

---

## 2. Create a Task

* Endpoint: `POST /tasks`
* Status received: `201 Created`
* Initial task status: `pending`
* Matches OpenAPI specification: Yes

### Request

```json
{
  "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
  "title": "Write the quickstart guide",
  "description": "Create a guide showing the complete TaskFlow workflow."
}
```

### Response

```json
{
  "task_id": "b7a0263b-7078-4f65-82a6-6261d0087837",
  "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
  "title": "Write the quickstart guide",
  "description": "Create a guide showing the complete TaskFlow workflow.",
  "status": "pending",
  "created_at": "2026-07-31T16:20:21.800Z",
  "updated_at": "2026-07-31T16:20:21.800Z"
}
```

### Notes

The task was created with the default status of `pending`, as described in the OpenAPI specification.

The returned `task_id` was later used to update and delete the task.

---

## 3. List Tasks in a Project

* Endpoint: `GET /projects/{project_id}/tasks`
* Status received: `200 OK`
* Total tasks returned: `1`
* Matches OpenAPI specification: Yes

### Request

```http
GET /projects/58a61c80-6409-4ed7-a03d-0cace79fdf2f/tasks
```

### Response

```json
{
  "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
  "total": 1,
  "tasks": [
    {
      "task_id": "b7a0263b-7078-4f65-82a6-6261d0087837",
      "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
      "title": "Write the quickstart guide",
      "description": "Create a guide showing the complete TaskFlow workflow.",
      "status": "pending",
      "created_at": "2026-07-31T16:20:21.800Z",
      "updated_at": "2026-07-31T16:20:21.800Z"
    }
  ]
}
```

### Notes

The response included:

* The project ID.
* The total number of tasks.
* An array containing the project's tasks.

---

## 4. Update a Task Status

* Endpoint: `PATCH /tasks/{task_id}`
* Status received: `200 OK`
* Status tested: `in_progress`
* Matches OpenAPI specification: Yes

### Request

```json
{
  "status": "in_progress"
}
```

### Response

```json
{
  "task_id": "b7a0263b-7078-4f65-82a6-6261d0087837",
  "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
  "title": "Write the quickstart guide",
  "description": "Create a guide showing the complete TaskFlow workflow.",
  "status": "in_progress",
  "created_at": "2026-07-31T16:20:21.800Z",
  "updated_at": "2026-07-31T16:25:40.739Z"
}
```

### Notes

The task status was successfully changed from `pending` to `in_progress`.

The `updated_at` timestamp changed, while the original `created_at` timestamp remained unchanged.

---

## 5. Delete a Task

* Endpoint: `DELETE /tasks/{task_id}`
* Result: Task deleted successfully
* Matches OpenAPI specification: Yes

### Request

```http
DELETE /tasks/b7a0263b-7078-4f65-82a6-6261d0087837
```

### Response

```json
{
  "message": "Task 'b7a0263b-7078-4f65-82a6-6261d0087837' was successfully deleted."
}
```

### Notes

The API returned a confirmation message containing the ID of the deleted task.

---

# Validation and Error Tests

## 6. Create a Project Without a Name

* Endpoint: `POST /projects`
* Status received: `400 Bad Request`
* Matches OpenAPI specification: Yes

### Request

```json
{
  "description": "Project without a name."
}
```

### Response

```json
{
  "error": "Bad Request",
  "message": "The 'name' field is required and must be a non-empty string."
}
```

### Notes

The API correctly rejected the request because the required `name` field was missing.

The error message also clarified that the field must contain a non-empty string.

---

## 7. Create a Task With Missing Required Fields

* Endpoint: `POST /tasks`
* Status received: `400 Bad Request`
* Matches OpenAPI specification: Yes

### Request

```json
{
  "description": "Task request without the required fields."
}
```

### Response

```json
{
  "error": "Bad Request",
  "message": "The 'project_id' field is required."
}
```

### Notes

The request did not include either of the required fields:

* `project_id`
* `title`

The API reported the first missing required field, `project_id`.

---

## 8. Create a Task With an Invalid Project ID

* Endpoint: `POST /tasks`
* Status received: `404 Not Found`
* Matches OpenAPI specification: Yes

### Request

```json
{
  "project_id": "00000000-0000-0000-0000-000000000000",
  "title": "Invalid project test"
}
```

### Response

```json
{
  "error": "Not Found",
  "message": "No project found with project_id '00000000-0000-0000-0000-000000000000'. Create a project first using POST /projects."
}
```

### Notes

The API correctly prevented a task from being created without an existing project.

The response also provided a useful next step by directing the developer to create a project using `POST /projects`.

---

## 9. List Tasks in an Empty Project

* Endpoint: `GET /projects/{project_id}/tasks`
* Status received: `200 OK`
* Total tasks returned: `0`
* Matches OpenAPI specification: Yes

### Request

```http
GET /projects/58a61c80-6409-4ed7-a03d-0cace79fdf2f/tasks
```

### Response

```json
{
  "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
  "total": 0,
  "tasks": []
}
```

### Notes

The project continued to exist after its task was deleted.

The API returned an empty `tasks` array and a `total` value of `0`, rather than returning an error.

This behavior makes it possible to distinguish an empty project from a nonexistent project.

---

## 10. List Tasks for a Nonexistent Project

* Endpoint: `GET /projects/{project_id}/tasks`
* Status received: `404 Not Found`
* Matches OpenAPI specification: Yes

### Request

```http
GET /projects/00000000-0000-0000-0000-000000000000/tasks
```

### Response

```json
{
  "error": "Not Found",
  "message": "No project found with project_id '00000000-0000-0000-0000-000000000000'."
}
```

### Notes

The API correctly distinguished between:

* An existing project with no tasks.
* A project that does not exist.

---

## 11. Update a Task With an Invalid Status

* Endpoint: `PATCH /tasks/{task_id}`
* Status received: `400 Bad Request`
* Matches OpenAPI specification: Yes

### Request

```json
{
  "status": "completed"
}
```

### Response

```json
{
  "error": "Bad Request",
  "message": "Invalid status 'completed'. Allowed values are: pending, in_progress, done."
}
```

### Notes

The API rejected `completed` because it is not one of the supported task statuses.

The valid status values are:

* `pending`
* `in_progress`
* `done`

---

## 12. Update a Task Title and Description

* Endpoint: `PATCH /tasks/{task_id}`
* Status received: `200 OK`
* Matches OpenAPI specification: Yes

### Request

```json
{
  "title": "Updated task title",
  "description": "The title and description were updated successfully."
}
```

### Response

```json
{
  "task_id": "128395aa-1680-4359-ac5f-11efbd9624ac",
  "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
  "title": "Updated task title",
  "description": "The title and description were updated successfully.",
  "status": "pending",
  "created_at": "2026-07-31T16:43:33.598Z",
  "updated_at": "2026-07-31T16:46:15.538Z"
}
```

### Notes

The API updated the title and description in the same request.

The task status remained unchanged.

The `updated_at` timestamp changed to reflect the update.

---

## 13. Update a Task to Done

* Endpoint: `PATCH /tasks/{task_id}`
* Status received: `200 OK`
* Matches OpenAPI specification: Yes

### Request

```json
{
  "status": "done"
}
```

### Response

```json
{
  "task_id": "128395aa-1680-4359-ac5f-11efbd9624ac",
  "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
  "title": "Updated task title",
  "description": "The title and description were updated successfully.",
  "status": "done",
  "created_at": "2026-07-31T16:43:33.598Z",
  "updated_at": "2026-07-31T16:47:21.241Z"
}
```

### Notes

The task was successfully moved to the `done` status.

The API preserved the existing title and description while updating the task status.

---

## 14. Update a Task With an Empty Request Body

* Endpoint: `PATCH /tasks/{task_id}`
* Status received: `200 OK`
* Matches OpenAPI specification: No

### Request

```json
{}
```

### Response

```json
{
  "task_id": "128395aa-1680-4359-ac5f-11efbd9624ac",
  "project_id": "58a61c80-6409-4ed7-a03d-0cace79fdf2f",
  "title": "Updated task title",
  "description": "The title and description were updated successfully.",
  "status": "done",
  "created_at": "2026-07-31T16:43:33.598Z",
  "updated_at": "2026-07-31T16:48:06.341Z"
}
```

### Notes

The OpenAPI description states that at least one field must be provided when updating a task.

However, the live API accepted an empty object and returned `200 OK`.

The task's title, description, and status remained unchanged, but the API generated a new `updated_at` timestamp.

This behavior differs from the behavior described in the OpenAPI specification.

---

## 15. Delete an Already Deleted Task

* Endpoint: `DELETE /tasks/{task_id}`
* Status received: `404 Not Found`
* Matches OpenAPI specification: Yes

### Request

```http
DELETE /tasks/b7a0263b-7078-4f65-82a6-6261d0087837
```

### Response

```json
{
  "error": "Not Found",
  "message": "No task found with task_id 'b7a0263b-7078-4f65-82a6-6261d0087837'."
}
```

### Notes

After the task was deleted, a second delete request correctly returned a `404 Not Found` response.

---

# Authentication Tests

## 16. Send a Request Without Authentication

* Endpoint tested: `POST /projects`
* Authorization header: Not provided
* Status received: `401 Unauthorized`
* Matches OpenAPI specification: Yes

### Response

```json
{
  "error": "Unauthorized",
  "message": "Missing or invalid API key. Provide a valid key in the Authorization header as: Bearer <api_key>"
}
```

### Notes

The API correctly rejected the request when the `Authorization` header was missing.

---

## 17. Send a Request With an Invalid API Key

* Authentication method: Bearer token
* Status received: `401 Unauthorized`
* Matches OpenAPI specification: Yes

### Response

```json
{
  "error": "Unauthorized",
  "message": "Missing or invalid API key. Provide a valid key in the Authorization header as: Bearer <api_key>"
}
```

### Notes

The API returned the same response structure for an invalid API key as it did for a missing authentication header.

The response does not reveal whether the key was missing or invalid, which avoids exposing authentication details.

---

# Differences From the OpenAPI Specification

One difference was found between the provided OpenAPI specification and the behavior of the live API.

## Empty Task Update

The OpenAPI description states that at least one of the following fields must be included when updating a task:

* `status`
* `title`
* `description`

It also documents a `400 Bad Request` response for requests with missing update fields.

During testing, the following request was accepted:

```json
{}
```

The live API returned `200 OK` with the existing task data.

No task content changed, but the `updated_at` timestamp received a new value.

## Documentation Decision

The public-facing documentation should instruct developers to always provide at least one supported field when updating a task.

This follows the intended behavior described in the OpenAPI specification and avoids relying on the current empty-update behavior of the live API.

The testing notes record the live behavior as a known inconsistency.

---

# Final Testing Summary

The following areas were tested:

* Project creation.
* Required project fields.
* Task creation.
* Required task fields.
* Default task status.
* Task listing.
* Empty task lists.
* Nonexistent projects.
* Task title and description updates.
* Valid task status updates.
* Invalid task statuses.
* Task deletion.
* Repeated task deletion.
* Missing authentication.
* Invalid authentication.
* Empty task update bodies.

With the exception of the empty `PATCH` request, the tested behavior matched the provided OpenAPI specification.
