# TaskFlow API Documentation

Public developer documentation for the TaskFlow API, built with Mintlify and generated from the provided OpenAPI specification.

## Project links

- [Live documentation](https://mari-codes.mintlify.site/)
- [GitHub repository](https://github.com/mari-codes/taskflow-api-docs)

## About the project

TaskFlow is a task management API that allows developers to:

- Create projects
- Create tasks inside existing projects
- List tasks that belong to a project
- Update task titles, descriptions, and statuses
- Permanently delete tasks

This repository contains the public-facing documentation created for the TaskFlow Technical Writing Test.

## Documentation

The documentation is organized around the developer journey, from the first authenticated request to endpoint-level reference material.

### Getting started

- [Introduction](https://mari-codes.mintlify.site/) — Overview of the API, available operations, and resource workflow.
- [Quickstart](https://mari-codes.mintlify.site/getting-started/quickstart) — Complete the first project and task workflow.
- [Authentication](https://mari-codes.mintlify.site/getting-started/authentication) — Configure Bearer authentication and troubleshoot authorization errors.

### Core concepts

- [Projects and tasks](https://mari-codes.mintlify.site/concepts/projects-and-tasks) — Understand the relationship between projects, tasks, and their IDs.
- [Task lifecycle](https://mari-codes.mintlify.site/concepts/task-lifecycle) — Learn about task statuses and update behavior.

### Guides

- [Manage a task](https://mari-codes.mintlify.site/guides/manage-a-task) — Follow an end-to-end workflow from project creation through task deletion.

### Resources

- [Errors and troubleshooting](https://mari-codes.mintlify.site/resources/errors) — Review error formats, HTTP status codes, and common solutions.

### API reference

- [API overview](https://mari-codes.mintlify.site/api-reference/overview) — Review API conventions, resource formats, authentication requirements, and the recommended endpoint order.

The interactive endpoint reference is generated from `openapi.yaml` and organized into two resource groups:

- Projects
- Tasks

It includes all five TaskFlow API operations.

## Run locally

### Prerequisites

- Node.js
- Mintlify CLI
- Git

Install the Mintlify CLI:

```bash
npm install -g mint
```

Clone the repository:

```bash
git clone https://github.com/mari-codes/taskflow-api-docs.git
cd taskflow-api-docs
```

Start the local documentation server:

```bash
mint dev
```

Open the local site in your browser:

```text
http://localhost:3000
```

## Validate the project

Validate the documentation configuration and OpenAPI definition:

```bash
mintlify validate
```

## API testing

The live API was tested manually with Postman before the documentation was written.

The tested workflow included:

1. Creating a project
2. Creating a task
3. Listing a project's tasks
4. Updating a task
5. Deleting a task

Additional tests covered:

- Missing and invalid authentication
- Missing required fields
- Invalid project IDs
- Empty task lists
- Nonexistent projects
- Invalid task statuses
- Task title and description updates
- Repeated task deletion
- Empty task update requests

Detailed results are available in the [API testing notes](internal/testing-notes.md).

## Known API discrepancy

The OpenAPI specification states that `PATCH /tasks/{task_id}` requires at least one of the following fields:

- `status`
- `title`
- `description`

During live testing, the API accepted an empty JSON object, returned `200 OK`, and updated only the `updated_at` timestamp.

The public documentation follows the intended OpenAPI contract and instructs developers to provide at least one supported update field.

## Repository structure

```text
taskflow-api-docs/
├── api-reference/
│   └── overview.mdx
├── concepts/
│   ├── projects-and-tasks.mdx
│   └── task-lifecycle.mdx
├── getting-started/
│   ├── authentication.mdx
│   └── quickstart.mdx
├── guides/
│   └── manage-a-task.mdx
├── internal/
│   ├── submission-summary.md
│   └── testing-notes.md
├── resources/
│   └── errors.mdx
├── docs.json
├── index.mdx
├── openapi.yaml
└── README.md
```

The `internal` directory contains project notes and submission material. It is excluded from the public Mintlify navigation.

## Security

Public documentation examples use a placeholder instead of displaying an API key:

```http
Authorization: Bearer YOUR_API_KEY
```

API keys should be stored securely and should not be added to public examples or client-side applications.

## Built with

- Mintlify
- OpenAPI 3.1
- MDX
- GitHub
- Postman
