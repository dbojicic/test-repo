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

##### MedicationDispense `whenPrepared` precision

This test series tests the AU Core invariant requiring `MedicationDispense.whenPrepared`, when populated, to be precise to at least the day: `$this.hasValue() implies $this.toString().length() >= 10`

The tests use profiles derived from AU Core MedicationDispense with different downstream constraints on whenPrepared. The purpose is to confirm that the invariant constrains date precision without preventing downstream profiles from independently constraining the presence of whenPrepared, use of Data Absent Reason (DAR), or other valid profiling choices.

The following test profiles are used:

Profile | Additional constraint
--- | --- 
`au-core-medicationdispense-wp-a`|Derives from AU Core MedicationDispense with no additional constraints
`au-core-medicationdispense-wp-b`|`whenPrepared` is mandatory and is required to have a value
`au-core-medicationdispense-wp-c`|`whenPrepared` is mandatory; DAR is permitted
`au-core-medicationdispense-wp-d`|`whenPrepared` is mandatory and a separate invariant requires either a value or DAR
`au-core-medicationdispense-wp-e`|`whenPrepared` remains optional and a separate invariant prohibits DAR
`au-core-medicationdispense-wp-f`|Derives from profile C with no additional constraints on `whenPrepared`, to test inheritance through a second level of derivation
{:.grid}