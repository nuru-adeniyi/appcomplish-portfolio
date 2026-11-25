Guide the user through drawing a project's AWS architecture diagram in draw.io, then put it on the page.

Usage: `/diagram <slug>` — e.g. `/diagram s3-static-website`. By default the user draws and you advise and fill the page. If the user asks you to draw it, skip the draw.io tutorial and follow **Draw it yourself** below. Never touch AWS.

1. Read `src/projects/<slug>.html` (and the CI/CD or build notes the user gives). List ONLY the AWS components that were built, in the order a request or deployment travels. Ask the user to confirm the list before giving steps. If a component was not built, leave it out; never draw an example or a "might add later" service.
2. Give the steps below as a short numbered tutorial, with the project's own components filled in.
3. When the user sends the exported file path, create `src/assets/images/projects/<slug>/` if it is missing, copy the file there as `architecture.png` (or `.svg`), replace the `diagram-slot` div with an `<img>` (relative `../assets/...` path, `alt` that spells out the flow and acronyms), and write the caption: Figure 1, what it shows and why this design fits. Then run `/changes`. Do not stage, commit or push.

## Standard (every project uses the same layout)

**Boxes: only what was built, outermost first.**
1. **AWS Cloud** around everything built. The visitor or user (laptop icon, labeled "Visitor (browser)") sits outside it, to the left.
2. **AWS account** inside AWS Cloud.
3. Inside the account, side by side: **Global services** (Route 53, CloudFront and other services with no Region) and **Region `<region>`** (S3, ACM for CloudFront in `us-east-1`, and other regional services).
4. Add a VPC, Availability Zone or subnet box only if the project created one. Never draw a VPC "as an example".

**Components:** one AWS icon each (draw.io: More Shapes → AWS), with the service name under it and a short detail label (bucket name, record type, "Block Public Access on"). Place them left to right in request order.

**Arrows:** numbered 1, 2, 3... in the order things happen, each with a short label. Request = solid, response/return = dashed, supporting link that is not in the path (such as a certificate) = dotted with no number. Attach arrows to the icons (drag from the blue hover arrows) so they follow when moved. Give each arrow its own spot on an icon so lines do not overlap. A feature that sits between two services (for example Origin Access Control) is a label on that arrow, not a floating text box.

**Look:** white canvas with the official AWS colors (the user's choice for the Serverless Web Application diagram; use it for new diagrams unless told otherwise). It shows as a white panel on the dark page. Dark text, grid off for export, spelling checked.

**Export:** PNG at 2x or SVG, named `architecture.png` / `architecture.svg`, and keep the file small.

**Page rules:** the diagram is the only copy of the flow, so the caption says why the design fits and does not repeat the arrows. CI/CD pages have no architecture slot. If a component changes later, redraw and re-export; do not patch the picture with text.

## Draw it yourself (SVG with the official AWS icons)
Proven on `serverless-web-application` (built once, reuse this method). Still confirm the component list with the user first (step 1), and never use a vendor's slide or screenshot: course material such as Digital Cloud Training's is copyrighted, so redraw from what the user built.

1. **Get the current icons.** Fetch `https://aws.amazon.com/architecture/icons/` (curl with a browser User-Agent) and grep it for the `Icon-package_<MMDDYYYY>.<hash>.zip` link under `d1.awsstatic.com/.../architecture-icon-release/` (the name changes each release; July 2026 was `Icon-package_07312026`). Download it into a NEW empty folder in the scratchpad and keep scripts elsewhere. The icons are free to use in architecture diagrams, unmodified and without implying AWS endorsement.
2. **Extract only what is needed** with `unzip -j <zip> <path> -d <out>`:
   - Services: `Architecture-Service-Icons_<date>/Arch_<Category>/48/Arch_<Service-Name>_48.svg`, for example `Arch_Compute/48/Arch_AWS-Lambda_48.svg`, `Arch_Networking-Content-Delivery/48/Arch_Amazon-API-Gateway_48.svg`, `Arch_Application-Integration/48/Arch_Amazon-Simple-Queue-Service_48.svg`, `Arch_Storage/48/Arch_Amazon-Simple-Storage-Service_48.svg`, `Arch_Databases/48/Arch_Amazon-DynamoDB_48.svg`. List the zip with `unzip -l <zip> | grep -i <name>` to find others.
   - Groups: `Architecture-Group-Icons_<date>/AWS-Cloud_32.svg`, `AWS-Account_32.svg`, `Region_32.svg` (40 px boxes).
   - Visitor: `Resource-Icons_<date>/Res_General-Icons/Res_48_Light/Res_Client_48_Light.svg`. Use the `Light` file on a white canvas (dark glyph); `Dark` has a white glyph.
3. **Build one standalone file** `src/assets/images/projects/<slug>/architecture.svg` with a short Python script run as `python -I` (it only reads the icon files as data). For each icon: drop the XML declaration, the root `<svg>` tag, `<title>` and every `id="..."`, then wrap the rest in `<g transform="translate(x y) scale(size/viewBoxWidth)">`. The icons have no gradients or `<defs>`, so ids can be dropped safely. Service icons are 64 px (viewBox 64), group icons 40, client 64 (viewBox 48). Use `font-family="Arial, Helvetica, sans-serif"` (an SVG shown through `<img>` cannot load page fonts) and no CSS variables, since the file must not depend on the page.
4. **Layout that worked** (viewBox about 900 wide, white `<rect>` first): Visitor icon at left, outside; nested group rectangles square-cornered with the group icon at the top-left and a bold label beside it: AWS Cloud navy `#232F3E` solid, AWS account pink `#E7157B` solid, Region teal `#00A4A6` dashed with a very light tint fill. Services in a snake: row 1 left to right, an elbow arrow down the right edge, row 2 right to left. Icon 64 px, name bold 15 px under it, up to three 12.5 px detail lines in gray `#545B64`. Arrows navy 2 px with a triangle marker; response dashed; numbered badges (navy circle, white number) on each arrow; a legend of one line per three steps under the diagram. Keep badges at least 12 px away from group borders.
5. **Rules learned.** A label sits under each icon, so never run an arrow straight down from an icon through its own label: leave from a side and use an elbow. Split long resource names over two lines (about 20 characters per line at 12.5 px) so they are not clipped by arrows. Route the response arrow above the Region label. Only arrows between neighbours need a badge in the gap.
6. **Check it in a browser, not by eye on code.** The browser tool blocks `file:` addresses. Start `python -I -m http.server 8765 --bind 127.0.0.1 --directory src` in the background, open `http://127.0.0.1:8765/...` with the Playwright tools, save screenshots under the repo's `.playwright-mcp/` folder (the only allowed place), and read them. Check the SVG alone, then the page at about 1100 px and 375 px. Fix clipping and overlaps and repeat. When done: stop the server (`netstat -ano | grep ":8765"` then `taskkill //PID <pid> //F`) and delete `.playwright-mcp/` so it is never committed.
7. **Put it on the page.** Replace the `diagram-slot` div with a plain `<img src="../assets/images/projects/<slug>/architecture.svg" width height alt>`; no link wrapper, because `main.js` already adds a full-screen expand button to every `.diagram-box img` (the way phone visitors read small labels). The `alt` spells out each numbered step and the acronyms. Rewrite the caption as Figure 1, why the design fits, without repeating the arrows. Then run `/changes`.

## Reference
`src/projects/s3-static-website.html` — Visitor → Route 53 → CloudFront → (OAC) → private Amazon S3 bucket, with the ACM certificate dotted to CloudFront.

`src/projects/serverless-web-application.html` — the self-drawn SVG with official AWS icons (white canvas, numbered arrows, legend), built with the "Draw it yourself" method above.
