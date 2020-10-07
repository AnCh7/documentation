# Continuous Integration, delivery and deployment

![image](.ci-cd-images/ci%20cd%20asset%20updates%20.007.png)

### Continuous Integration

- Developers merge their changes back to the main branch as often as possible.
- Changes are validated by creating a build and running automated tests against the build.

Benefits:
- less bugs (captured early by the automated tests).
- building the release is easy (integration issues have been solved early).
- less context switching (devs are alerted as soon as they break the build).
- CI server can run hundreds of tests in the matter of seconds.
- QA team spend less time testing.


### Continuous delivery

- Automated release process and you can deploy your application at any point of time.

Benefits:
- no complexity of deploying software.
- release more often (gather feedback).

### Continuous deployment 

- Every change that passes all stages of your production pipeline is released to your customers. There's no human intervention.

Benefits:
- no need to pause development for releases.
- releases are less risky and easier to fix.
- customers see a continuous stream of improvements.