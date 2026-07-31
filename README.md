# TaskFlow API Documentation

Public developer documentation for the TaskFlow API, built with Mintlify and generated from the provided OpenAPI specification.

## Project links

- Live documentation: ``
- GitHub repository: `https://github.com/mari-codes/taskflow-api-docs`

## About the project

TaskFlow is a simple task management API that allows developers to:

- Create projects
- Create tasks inside existing projects
- List tasks that belong to a project
- Update task titles, descriptions, and statuses
- Permanently delete tasks

This repository contains the public-facing documentation created for the TaskFlow Technical Writing Test.

## Documentation structure

The documentation is organized around the developer journey.

### Getting started

Introduces the API and helps a developer complete the first successful workflow.

- Introduction
- Quickstart
- Authentication

### Core concepts

Explains how the API resources behave.

- Projects and tasks
- Task lifecycle

### Guides

Provides task-oriented instructions for completing a full workflow.

- Manage a task

### Resources

Helps developers understand and troubleshoot failed requests.

- Errors and troubleshooting

### API reference

The endpoint reference is generated from `openapi.yaml` and grouped by resource:

- Projects
- Tasks

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

Validate the documentation configuration:

```bash
mint validate
```

Validate the OpenAPI specification:

```bash
mint openapi-check openapi.yaml
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
- Repeated task deletion
- Empty task update requests

Detailed testing notes are available in:

```text
internal/testing-notes.md
```

## Known API discrepancy

The OpenAPI specification states that `PATCH /tasks/{task_id}` requires at least one of these fields:

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

## Security

The API key used for live testing is not included in this repository.

Public examples use:

```http
Authorization: Bearer YOUR_API_KEY
```

## Built with

- Mintlify
- OpenAPI 3.1
- MDX
- GitHub
- Postman
