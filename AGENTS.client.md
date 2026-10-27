## Core Principles

- Keep things simple, concise, clear, and explicit
- Avoid code duplication by abstracting repeated logic into reusable code
- Avoid simply piling up code; use proper abstractions to maintain code maintainability and extensibility
- When completing a task, do not make only superficial changes; update related logic, tests, and documentation as well. When removing something, ask whether its related content should also be removed

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

- Temporarily store all assets in the `tmp` directory at the project root.
- Export icon assets as SVG and wrap them as components in `@zingmake/components/icons`.
- Export decorative images as 2x PNG, convert them to WebP using the system-installed `ffmpeg`, and name them using UUIDs in the format `uuid.webp`. Then, use the locally installed `ossutil` to upload them to the `tmp` directory in the `gearx` bucket. Credentials such as `accessKeyID` have already been configured in the configuration file.
- Use placeholders for all asset references in the code, such as `<img src="uploaded-image-url" />`.

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
