## Core Principles

- Keep things simple, concise, clear, and explicit
- Avoid code duplication by abstracting repeated logic into reusable code
- Avoid simply piling up code; use proper abstractions to maintain code maintainability and extensibility
- When completing a task, do not make only superficial changes; update related logic, tests, and documentation as well. When removing something, ask whether its related content should also be removed

## Docs

- Store documents created by the Agent during implementation in the `docs/` directory at the project root, organized into these categories:
  - `docs/design/`: Design decisions, including architecture and feature designs.
  - `docs/development/`: Documents for continued development by Agents, including TODOs and implementation records.
  - `docs/features/`: Feature descriptions for future product documentation, technical documentation, and the official website.
- Each category directory must contain a `README.md` as its document index, with relative links and brief descriptions. Update the index whenever documents are added, moved, renamed, or deleted.
- Store temporary files, including validation results, benchmark reports, and screenshots, in the `tmp/` directory at the project root.
- Files in `tmp/` may be deleted at any time. Record any conclusions or information needed for continued development or future documentation in the appropriate `docs/` category; do not rely on files in `tmp/` for long-term reference.

## Code Style

- Keep symbol names as concise as possible while preserving semantic clarity within their context
- Leave at least one blank line between code blocks
- Leave at least one blank line between comments and the code above them
- Use the `a.b.c` naming format for storage keys

## Testing

- Add necessary unit tests, integration tests, browser automation tests, and performance tests

## Comments

- Add detailed comments to code that is difficult to understand
- Do not add comments for self-explanatory code

## Assets

- Store all assets in the tmp directory at the project root.
- Export icon assets as SVG and encapsulate them as components in @artibox/components/icons.
- Export decorative images as 2x PNG, convert them to WebP using the system-installed ffmpeg, and name them uuid.webp using UUIDs.
- Use placeholders for all asset references in the code, such as <img src="uuid.webp" />.

## APIs

- When I explicitly indicate that an API is not yet implemented, mock the corresponding API using Next.js API routes with the `/api/mock` path prefix

## Component Usage

- Reuse components from the existing component library whenever possible

## Performance

- Performance is a high priority in this project; always use the most efficient implementation while meeting functional requirements
- Always use dynamic imports for pages or components that do not require SEO
- Always create modal components dynamically rather than injecting them statically

## Desktop Application

- `apps/desktop` is reserved for future expansion and should not be implemented at this stage; however, code logic sharing between Web and Desktop must be considered from the beginning

## Backward Compatibility

- This project is still under development; backward compatibility with historical data or logic is not required
- Do not add tests for removed functionality
