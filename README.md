_Work in Progress_

Open Source Software (OSS) General Documentation
==
This repository contains common documentation applicable to all of my repositories with the "oss-" prefix. This documentation should help explain the overall objectives, reasoning, and organization of these repositories.

## Overview
All "oss-" prefixed repositories belong to the group of software I have made available as open source. These projects are typically licensed under the MIT license but as always please view the license file in each repository. I am providing these projects as-is. I support these projects in my spare time as a hobby so support may not exist. You may use them according to their license but otherwise do not expect support from me. I am releasing these projects mostly a portfolio of sorts but also as examples of how solutions could be implemented.

The "oss-commons-" repositories are a collection of libraries collectively grouped as my "commons" projects. Unfortunately GitHub does not support the concept of grouping projects otherwise these projects (and the other OSS projects) would have been organized using groups instead of encoding the grouping into the repository name.

## What is a library?
The goal of these libraries is to be a minimal, reusable set of functionality. As much as it makes sense to do so each library is focused on a small set of tightly coupled functionality that has minimal external dependencies. The "do one thing and do it well" approach, within reason.

## Why split each library into it's own repository?
While it would be possible to organize these libraries into a shared "commons" repository (e.g. a multi-module Maven project) that would require (or at least strongly encourage) a synchronized release process. As these projects have stabilized, the overhead of rebuilding and releasing the entire collection just because a single module has a change (or a new module is added) makes less and less sense. While it's initially more work to setup split repositories for each component, the long term benefits are more focused release and maintenance cycles and smaller impacts to downstream projects as only the updated component is changed. This process is facilitated by a fully automated build, test, and release pipeline - once configured the CI/CD pipeline manages everything else.

## Test Coverage
Unless specifically documented, all projects are required to have 100% test coverage. If the design is not testable then alternative designs shall be considered. If a statement or code path exists then it's possible for the program to execute it therefore there should be a test asserting what should happen in that scenario.

What are some exceptions to this? Well, Java's legacy designs (mostly around unchecked exceptions) does leave some corner cases where native API calls that are impossible to mock or otherwise force certain exceptions do exist. These are rare (usually involving some form of I/O related activity) and when encountered may not be possible to avoid. In such cases these functions shall be isolated to minimal libraries so that coverage exceptions can be isolated and well documented.

Why 100% and not simply 90% or some other number? It is true that reaching 100% may seem like a lofty goal but the reality is that if you write _good_ tests (i.e. tests that actually process code rather than just filler tests) that achieve 100% statement and path coverage your project now gains a very sensitive tripwire that can detect any change in the code that is now _not_ tested. Consider a project with a lot of code (thousands and thousands of lines of code). If such a project has less than 100% coverage it's possible write several lines of code that does not affect the coverage metric due to the volume of code being tested. The larger the projected the larger the missed code segment can be - entire classes could be omitted in large enough projects (which isn't all that large in practice). Now instead of this same project has 100% coverage then any single statement or path not execute during testing will drop the coverage level to below 100% which is very easy to detect. This is also another reason for keeping each component small - smaller is easier to test and ensure that every possible execution path is exercised.

The other benefit of full test coverage is that the pipeline can and will execute every documented scenario every time - if it's worth testing then it worth automating. This gives confidence that changes made to the project won't break existing and supported functionality in unintended ways.

Does this mean full coverage will catch every interesting scenario? Not exactly. It might in simple projects but in practice the coverage tools measure each statement and branch as though it were an independent variable. The reality is that often times these statements and especially branches are conditionally linked to one another. Likewise with the untestable designs mentioned above, it's always possible for the program to terminate unexpectedly (e.g. pull the power and program immediately halts) so there is never a guarantee that any given statement is properly executed so clean up logic and error handling may not always happen. For this reason full statement and path coverage is considered the minimal required coverage and any interesting use cases shall also be added a scenarios even if they technically produce redundant coverage (this also helps ensure supported functionality operates according the contract even if the underlying implementation changes drastically).

## Code Style
Any contributions shall conform the project's existing style, formatting, and naming convention.

## Contributions
Submissions to enhance existing projects may be made via a Pull Request; however, as these are hobby projects there is no timeline commitments. All final decisions regarding these projects shall be at my discretion.

## Feature Requests/Bugs
If you found a bug, vulnerability, or have a feature request you may create an issue in the appropriate project's issues section. These projects are supported as a hobby so there is no timeline commitment.

## External Dependencies
For basic libraries (e.g. stand-alone functionality such as hashing, encoding, etc.) the use of 3rd party dependencies shall be avoided instead favoring pure JVM core components only. 

For framework libraries (e.g. Spring based libraries) 3rd party dependencies shall be kept to the minimum needed to support the component (i.e. don't use a project's BOM when a specific component will do and don't pull in more than is absolutely needed).

The goal is keep these libraries as free from external dependencies as is reasonably possible. This helps reduce runtime conflicts as well as reduces the maintenance needed to keep external components up to date, especially when security patches are involved.

A classic example to avoid is pulling in the Apache Commons Lang3 library only to use StringUtils.isBlank() in a small number of places when manually checking null/blank would suffice. Yes, it might add a few extra test cases to a class here and there but that is preferred over bringing in an entire 3rd party dependency that only has minimal usage. (By the way, these test case are likely to be needed anyway as the test case for null/empty/blank inputs would be considered a part of the API contract regardless if the functionality was implemented manually or delegated to a library.)

## Shaded Components
Creation of shaded components is forbidden - a well designed library should never embed 3rd party content inside itself. The primary reasons are that this can introduce runtime side-effects (it in effect allows parallel copies of different versions of the same component to coexist) and it makes patching security issues very difficult. In general, even introducing dependencies that themselves are or depend on shaded artifacts should be avoided as much as possible.
