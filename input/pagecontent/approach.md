### Testing approach

This IG contains test profiles and examples used to test FHIR profile behaviour, including the behaviour of profiles when further constrained in downstream profiles.

Tests may include both examples expected to pass validation and examples expected to fail validation. Where an example is expected to fail, the test identifies the constraint expected to cause the failure.

#### Testing conventions
- Test profiles within a test series use a suffix (-a, -b, etc.) to identify each profiling variation.
- Examples used to test a profile carry the same suffix as the profile.
- Each example has a number (01, 02, etc.) identifying the test scenario for that profile.
- Example ids use the pattern `<resource>-<test-series>-<suffix>-<scenario>`.
- Each example declares conformance to the test profile with the corresponding suffix in `meta.profile`.
- Each test series defines its test-series code and documents the profiles, scenarios and expected validation results.

For example, for the `MedicationDispense.whenPrepared` test series, `wp` is the test-series code:
- profile: `au-core-medicationdispense-wp-a`
- examples: `medicationdispense-wp-a-01`, `medicationdispense-wp-a-02`, etc.

#### Test series

The following test series use the testing approach and conventions described above:

- [MedicationDispense.whenPrepared precision](meddisp-whenprepared.html)
- [MedicationRequest authoredOn date or Data Absent Reason](medreq-authoredon.html)