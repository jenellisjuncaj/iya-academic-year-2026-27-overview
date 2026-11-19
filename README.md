# Academic Year 2026–27 Enrollment Overview

A separate static dashboard for USC Iovine and Young Academy. Existing semester repositories and Netlify sites are unchanged.

## Start here

1. Open the standalone preview in the adjacent outputs folder: Academic_Year_2026_27_Preview.html. It works by double-clicking and contains the verified snapshot; it does not refresh itself.
2. Create a NEW GitHub repository named iya-academic-year-2026-27-overview. Do not create it from the fall template. A blank repository is easiest.
3. Upload this project's CONTENTS to its root. The root should contain index.html, styles.css, app.js, overview.js, data.json, sources.json, tests.js, netlify.toml and this README. Keep scripts/refresh-data.js inside scripts. Keep .github/workflows/refresh-data.yml inside .github/workflows.
4. If using GitHub's file picker, upload the root files first. Then use Add file → Create new file and enter scripts/refresh-data.js as the filename, paste that local file's contents, and commit. Repeat for .github/workflows/refresh-data.yml. Alternatively drag the scripts and .github folders into the root upload area, preserving their paths. Do not flatten them into the root.
5. In Actions, enable workflows if requested. Choose Refresh academic year snapshots → Run workflow → main. Wait for a green check. Source failures are recorded in data.json and shown on the page; inspect the semester status even if the workflow is green.
6. In Netlify: Add new project → Import an existing project → GitHub → select the NEW overview repository. If it is missing, Configure the Netlify app on GitHub and add this repository while retaining existing selections.
7. Branch: main. Base directory: blank. Build command: node scripts/refresh-data.js. Publish directory: a single period (.). netlify.toml supplies these build settings. No npm install, framework, secrets, functions, or environment variables are needed. Select Node 22 or newer if the Netlify build image requests a version.
8. Deploy, then open the new URL. Verify title, semester dates, matching reconciliation rows, course search, and the two semester dashboard links.

## Data refresh

Semester workflows continue to fetch USC's CoursesByTermSchoolProgram API with school=ACAD and programs ACAD, IDSN, PRIN. Fall uses termCode=20263; spring uses termCode=20271. Their existing schedules target around 6 a.m. Pacific, with GitHub scheduling delays possible.

This project imports each source repository's committed data.js at an exact commit SHA, preserving its enrollment counts, credit map and source snapshot date. No independent USC enrollment query is added. Its hourly GitHub Actions schedule runs at :25 UTC and commits data.json, triggering Netlify. The Netlify build also imports the semester snapshots. The app reads local JSON, not executable external scripts. Importing the sources requires public access to both repositories.

A source failure retains the last good data and sets an error badge. A missing source is not treated as zero; the annual total remains unavailable. Outdated means the SOURCE refresh is more than 48 hours old. Unknown dates are flagged. ImportedAt is never used to make source data appear current. GitHub may disable schedules after prolonged public-repository inactivity; check Actions if updates stop. Hourly deployments consume your normal Netlify build allowance.

## Calculation policy

See SOURCE_AUDIT.md for the pre-combination audit, exact verified totals and limitations. Each section identifier includes semester and section number. Common inclusion: numeric nonnegative enrollment, positive capacity, ACAD/IDSN/PRIN, source metrics flag not false, and not explicitly cancelled. Duplicate contextual cross-listings count once. Missing counts are excluded with a reason and reconciliation drift is flagged.

Course Enrollments are registrations, not unique students. Available Seats = sum of max(capacity - enrollment, 0). Capacity Utilization = sum of enrolled seats / sum of total seat capacity, as clarified by the user. Annual SCH preserves each source's fixed-unit map without estimating variable/unknown units. Course prefix + full number including suffix is the grouping key; title changes are listed together. A semester with no counted offering is shown as Not offered, a missing semester as Unavailable, and a reported zero as 0.

## Development and verification

Node 22+, no dependencies:

    node scripts/refresh-data.js
    node tests.js
    node scripts/serve.js

Open http://127.0.0.1:8766. The initial delivery passed 16 calculation/importer tests. Both deployed semester data.js payloads matched their repository snapshots at inspection. Browser checks covered search, prefix filters, reset, sorting and no console errors at the desktop viewport. Responsive CSS is included; a mobile viewport was not available in the browser test environment.

Future source rule changes may produce a reconciliation warning. Tests validate that warnings accurately describe differences; they do not silently force totals to match. Review the source semester change before revising inclusion policy.
