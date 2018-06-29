#### [Docs](../../) / [About fabrix](./) / Release Schedule

# 10.3. Release Schedule

The fabrix team maintains this document to serve as a foward-looking plan for how the framework will grow and evolve. The goal of the roadmap is to assist developers in planning the future of their projects. Please submit an Issue or Pull Request if you feel that something should be added or changed in this document, and we are happy to discuss.

## Organization

Each major and minor release will be represented by a Github Milestone. Each major version series will be developed on a separate branch. Please submit featyre requests, bug reports, and other suggestions as Github Issues for inclusion in a future release.

## Release Schedule

fabrix intentionally parallels its release schedule with the [Node.js release schedule](https://github.com/nodejs/LTS#lts-plan).

![fabrix Release and Maintenance Roadmap](https://s3.amazonaws.com/fabrix.app/images/fabrix+Maintenance+Schedule+v4.5.png)

Major and minor version releases occur according to a regular calendar schedule, and versions are assigned according to semver. Major releases occur in April. The fabrix team releases a minor version once per month with the latest feature updates for the current series. The fifth minor release, in October of the same year, will be tagged for LTS (Long Term Support) which lasts 18 months.

## Upcoming Releases

### v3.0 (April 2017)

fabrix v3 development takes place on the [v3 branch](https://github.com/fabrix-app/fabrix/tree/v3). Some key changes and additions:
- Node 6 is required; ES5 code written for Node 4 will be refactored into ES6
- Separate i18n and logging from the core
- Utilize GraphQL as a common ORM language
