# Android specific CI materials
The Android app and ecosystem means the design and implementation of the CI for the Android app will be distinct and have particular challenges and solutions. This page covers what we think and understand about these topics.

## Build Platform (CI)
We need a build platform (CI) that's suited to build Android apps including C++ code using the Android NDK. As mentioned in the main [README.md](../README.md) file the build environment needs network connectivity to servers on the internet. We also need the environment to support the addition of various Linux packages and tools, and these need to meet various minimum versions, etc.

The codebase incorporates nested subprojects and therefore the CI needs to be able to checkout and process these. Travis-ci, for example by default automatically processes sub-projects but does not seem to process their respective sub-projects by default; we've found a way to make this work.

Application and end-to-end tests need to run in an Android device, most likely a virtual device as provided by an Android emulator. Therefore the CI environment needs to support the creation of a virtual device, deployment of the main and test apps, test execution, and test reporting. We may also want it to generate code coverage reports (sometimes the process is complicated, especially for projects such as ours with native code and nested sub-projects, etc.)

As our app provides connectivity services for other apps, we probably need ways to generate traffic from another app - either one we create, one that's pre-installed in the emulator (e.g. a web browser), or third-party that would be installed as part of the setup of the test environment.

Perhaps we could have a robot-like test app that - once started - automatically makes network requests and checks the results rather than needing to write lots of code in a test automation framework such as UI Automator? Also, we could consider adding an inter-process communications mechanism so the test app can query whether a VPN has been established, and if not, what the current state of the connection is.

## Levels of testing
We would like our tests to exercise as much of the application's functionality and behaviour as practical. Because of the potentially conflicting challenges of comprehensive testing vs. test execution time we may want/need to separate some of the more involved tests and run these for a subset of CI activities e.g. for pull requests.

### Thoughts on levels of testing
1. Unit-tests: Some initial confidence can be obtained from having and running unit tests. These might not need an Android runtime for pure Java/Kotlin/C++ classes.
2. Android tests: There are various smallish tests available that use Android-specific test runners. These may be useful complimentary tests to other levels of automated tests and run relatively quickly. 
3. Instrumentation-tests: These are bundled into a sibling test-app that interacts with the application being tested. The most popular test automation framework for these tests is (Android Espresso)[https://developer.android.com/training/testing/ui-testing/espresso-testing]. These tests are restricted to the application's process boundary.
4. Multi-application tests: Frameworks such as (UI Automator)[https://developer.android.com/training/testing/ui-automator] facilitate multi-app test automation. Android Devs provide a useful guide (Test UI for multiple apps)[https://developer.android.com/training/testing/ui-testing/uiautomator-testing]).

#### Testing of sub-projects functionality
There are various sub-projects our Android app relies on, each may have their own testing practices and automated tests. We may wish to check these tests and test results (for instance, flaws in these codebases could adversely affect the performance and trustworthiness of our apps). We may wish to contribute additional tests to those projects, and we may wish to include some tests of those projects within this and/or related codebases.

## CI execution time
Fast executions and results may help keep developers' attention on their code submission, PR, etc. The speed may also affect the throughput of the project when there are updates close together.

Initial experiments with using Travis-CI indicate the build process currently takes about 15 minutes which is significantly longer than we would wish. We have yet to establish a realistic target duration, as a start we can use a target of: the elapsed time < 10 minutes for a build to complete including the execution of unit tests.
