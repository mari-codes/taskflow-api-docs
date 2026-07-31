# Submission Summary

## Project links

- Live documentation: `https://mari-codes.mintlify.site/`
- GitHub repository: `https://github.com/mari-codes/taskflow-api-docs`

## Process

I started by reading the OpenAPI specification to understand the available resources, endpoint dependencies, request fields, response models, authentication requirements, and documented error cases.

Before writing the documentation, I tested the live API in Postman. I completed the full workflow by creating a project, creating a task, listing the project's tasks, updating the task, and deleting it. I also tested validation, authentication, empty-state, invalid-resource, invalid-status, and repeated-deletion scenarios.

I then created the Mintlify project from scratch, connected the OpenAPI specification to generate the API reference, and added supporting documentation based on the workflow a developer would follow when using the API for the first time.

Finally, I reviewed the navigation, internal links, request examples, response examples, and API reference pages locally before publishing the site.

## Structure and content decisions

I separated the documentation into four main areas:

- **Getting started** contains the overview, quickstart, and authentication guidance.
- **Core concepts** explains the relationship between projects and tasks, as well as the task lifecycle.
- **Guides** provides a complete task-oriented workflow that developers can follow from project creation through task deletion.
- **Resources** contains error handling and troubleshooting guidance.

The API reference is kept in a separate tab and generated from the provided OpenAPI specification. The endpoints are grouped by resource so readers can distinguish project-related operations from task-related operations.

I chose this structure to support different reader needs:

- A new developer can use the quickstart to complete the first successful workflow.
- A developer who needs more context can read the conceptual pages.
- A developer completing a specific task can follow the workflow guide.
- A developer looking for exact request and response details can use the API reference.

I also kept testing notes and submission material outside the public navigation. This separates user-facing documentation from internal project evidence and process notes.

## Testing findings

Most live API behavior matched the OpenAPI specification, including:

- Required project and task fields
- Default task status
- Supported status values
- Empty task lists
- Authentication errors
- Missing-resource errors
- Task updates and deletion

One inconsistency was found while testing `PATCH /tasks/{task_id}`.

The OpenAPI description states that at least one update field must be provided. However, the live API accepted an empty JSON object, returned `200 OK`, and changed only the `updated_at` timestamp.

The public documentation instructs developers to provide at least one supported field instead of relying on this behavior. The discrepancy is recorded in the repository's internal testing notes.

## Challenges

The main challenge was deciding how much supporting documentation was appropriate for an API with only five operations. I wanted the site to be useful and demonstrate clear information architecture without adding repetitive or unnecessary pages.

Another challenge was balancing the OpenAPI contract with the behavior observed during live testing. I treated the specification as the intended public contract while documenting the live discrepancy separately.

## What I would do with more time

With more time, I would:

- Add automated checks for broken internal links and OpenAPI validation.
- Add reusable variables or snippets to reduce duplication across cURL examples.
- Test the published documentation on additional screen sizes.
- Add a lightweight changelog and contribution guide.
- Work with the API team to clarify and resolve the empty update behavior.
