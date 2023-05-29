---
{"dg-publish":true,"permalink":"/development/unit-tests/","created":"","updated":""}
---


# Principles of a good test

- simple and readable
- tests one component (class, function, etc - one *unit*) at a time
- independent
- repeatable (known fixed input produces the same known fixed output)

# Structure

- **setup**: create needed objects and dependencies
    - external dependencies (database, network requests, etc) should be [[Development/Unit tests#Types of Mocks\|mocked]] so that they don't affect the test results
- **execute**: run the *unit* of code being tested
- **expect**: verify that the result matches what is expected

# Types of Mocks

- **stub**: returns the expected value with no other logic, ignores inputs
- **mock**: tests that it receives the expected inputs
    - ex. a method on the mock is called 5 times, and/or with the correct arguments
    - tied to implementation: if you change the code such that the method only needs to be called 1 time, the test will fail and need to be updated
- **fake**: a working implementation, but simplified from the production version
    - ex. an in-memory database
    - fakes can have their own suite of tests

# See Also

<div class="rich-link-card-container"><a class="rich-link-card" href="https://microsoft.github.io/code-with-engineering-playbook/automated-testing/unit-testing/mocking/" target="_blank">
	<div class="rich-link-image-container">
		<div class="rich-link-image" style="background-image: url('https://microsoft.github.io/code-with-engineering-playbook/resources/ms_icon.png')">
	</div>
	</div>
	<div class="rich-link-card-text">
		<h1 class="rich-link-card-title">Mocking in Unit Tests - Code With Engineering Playbook</h1>
		<p class="rich-link-card-description">
		CSE Code With Engineering Playbook
		</p>
		<p class="rich-link-href">
		https://microsoft.github.io/code-with-engineering-playbook/automated-testing/unit-testing/mocking/
		</p>
	</div>
</a></div>
