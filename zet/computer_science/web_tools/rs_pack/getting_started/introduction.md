# Introduction

Rspack (pronounched as `/'ɑrespæk/`, `ar-es-pa-k`) is a high performance
Rust-based JavaScript bundler that offers strong interoperability with
the [webpack](https://webpack.js.org/) ecosystem, enabling faster
development cycles and efficient collaboration between the two tools.

## Why Rspack?

Rspack was initially created to solve performance problems encountered
at ByteDance, a tech company that maintains many large monolithic app
projects with complex bundling requirements. Production build times had
grown to ten minutes or even half an hour in some cases, and cold start
times could exceed several minutes. After experimenting with many
bundlers and optimization ideas, a common set of requirements emerged:

* **Dev mode startup performance.** `npm run dev` is a commnd that
  developers may invoke many ties per hour. Engineering productivity
  suffers if startup time exceeds 10-15 seconds.
* **Fast builds.** `npm run build` is used in CI/CD pipelines and
  directly impacts merging productivity and application deliver time.
  Large applications may spend 20-30 minutes running these pipelines,
  and bundling time is often a major contributor.
* ** flexible configuration.** From experimenting with various popular
  bundlers, we found that one-size-fits all configurations encountered
  many problems when trying to accommodate real world projects. A major
  advantage of webpack is its flexibility and ease of accommodating
  customized requirements for each project. This in turn may pose steep
  migration costs for legacy projects that try to migrate away from
  Webpack.
* **Production optimization capabilities.** All of the existing bundling
  solutions also had various limitations when optimizing for a
  production environment, such as insufficiently fine-grained package
  splitting, etc. Rspack was an opportunity to rethink these
  optimizations from the ground up, leveraging Rust-specific features
  such as multithreading.

## Current Status of Rspack

Rspack had been under continuous development since April 2022, and was
launched as open source in March 2023. Although the project is still in
its early stages and lacks many webpack functionalities, based on the
Pareto principle, we believe the current features can meet the needs of
most businesses. Some ByteDance internal projects have already achieved
a 5-10 times improvement moving from Webpack to Rspack, and there's
still room for future optimizations.

Rspack is compatible with webpack's configuration schema and loader
architecture. You can seamlessly use familiar loaders such as
`babel-loader`, `less-loader`, `sass-loader`, etc. Our long-term goal is
to fully support most popular loaders including more complex cases such
as `vue-loader`.

Currently, Rspack's cache support is relatively simple, only supporting
memory-level caching. In the future, we will build stronger cache
capabilities, including migratable persistent caching, which will bring
greater imagination space, such as reusing Rspack's cloud cache on
different machines in monorepos, to improve the cache hit rate of large
projects.


