### Testing approach

This IG contains test profiles and examples used to test FHIR profile behaviour, including the behaviour of profiles when further constrained in downstream profiles.

Tests may include both examples expected to pass validation and examples expected to fail validation. Where an example is expected to fail, the test identifies the constraint expected to cause the failure.

#### Testing conventions
- Each test series uses a short code to identify its profiles and examples, e.g. wp for `MedicationDispense.whenPrepared` and ao for `MedicationRequest.authoredOn`.
Test profiles within a test series use a suffix (-a, -b, etc.) to identify each profiling variation.
- Additional codes or suffixes are used where needed to distinguish the profiles being tested within a test series.
- Examples used to test a profile carry the same suffix as the profile.
- Examples are numbered (01, 02, etc.) to identify the different test scenarios for a profile.
- Each example declares conformance to the test profile with the corresponding suffix in `meta.profile`.
- Each test series documents the profiles being tested, the test scenarios and the expected validation results.

For example, the `MedicationDispense.whenPrepared` test series uses profiles such as au-core-medicationdispense-wp-a, with examples medicationdispense-wp-a-01, medicationdispense-wp-a-02, etc.

#### Test series

The following test series use the testing approach and conventions described above:

- [MedicationDispense.whenPrepared precision](meddisp-whenprepared.html)
- [MedicationRequest authoredOn date or Data Absent Reason](medreq-authoredon.html)