### MedicationRequest.authoredOn date or Data Absent Reason

This test series evaluates four candidate FHIRPath expressions for the AU Core `MedicationRequest.authoredOn` invariant **au-core-medreq-01**, raised in [FHIR-59045](https://jira.hl7.org/browse/FHIR-59045).

The human readable invariant description is:
> Date shall be precise to the day or, if not available, the Data Absent Reason extension shall be present

The tests evaluate whether each candidate expression:
- requires a populated `MedicationRequest.authoredOn` value to be precise to at least the day
- requires Data Absent Reason (DAR) when `MedicationRequest.authoredOn` has no value
- determines how each expression behaves when a value and DAR are both present
- allows further constraints to be applied to `MedicationRequest.authoredOn` in downstream profiles

This will be used to determine which expression correctly implements the human readable description without intentionally restricting downstream use and profiling choices around `MedicationRequest.authoredOn` and the use of DAR.

#### Candidate expressions

The following candidate expressions are tested:

Series code|Candidate expression
--- | --- 
ao-a|`($this.hasValue() and $this.toString().length() >= 10) or ($this.hasValue().not() and extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists())`
ao-b|`($this.hasValue() implies $this.toString().length() >= 10) and ($this.hasValue().not() implies extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists())`
ao-c|`($this.hasValue() and $this.toString().length() >= 10) xor extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists()`
ao-d|`($this.hasValue() implies ($this.toString().length() >= 10 and extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists().not())) and ($this.hasValue().not() implies extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists())`
{:.grid}

The purpose of this test series is to confirm which expression(s) correctly implement the human readable description, without unintentionally restricting downstream profiling choices around authoredOn cardinality or use of DAR. 
This will provide input into discussion about which of the correct expressions is preferable for AU Core.

#### Initial expression comparison

Note: A FHIR primitive element can contain both a primitive value and extensions. The presence of a value and DAR on `MedicationRequest.authoredOn` should be considered when comparing the candidate expressions. 
**Question**: When a sufficiently precise value is present, does AU Core also require DAR to be absent? Think we need to build this check into the invariant expression.

The following table shows the expected result for each expression based on its FHIRPath logic:

Scenario | ao-a | ao-b | ao-c | ao-d|What we want
--- | --- | --- | ---
Precise value only|Pass|Pass|Pass|Pass|Pass
Imprecise value only|Fail|Fail|Fail|Fail|Fail
DAR only|Pass|Pass|Pass|Pass|Pass
Other extension only, no DAR|Fail|Fail|Fail|Fail|Fail
Precise value + DAR|**Pass**|**Pass**|Fail|Fail|Fail
Imprecise value + DAR|Fail|Fail|**Pass**|Fail|Fail
{:.grid}

The differences from the expression logic:

- **ao-a** requires either a sufficiently precise value, or no value with DAR present. When a sufficiently precise value is present, the expression passes regardless of whether DAR is also present. 
- **ao-b** requires a value, when present, to be sufficiently precise, and requires DAR when there is no value. No additional restriction on DAR when a value is present. Its expected results are the same as ar-or.
- **ao-c** requires exactly one side of the expression to evaluate to true. It fails a precise value with DAR. But - an imprecise value makes the first condition false; when DAR is also present, the second condition is true and the overall expression passes.
- **ao-d** requires a value, when present, to be sufficiently precise and DAR to be absent. When there is no value, it requires DAR to be present. It fails both a precise value with DAR and an imprecise value with DAR.

These scenarios are tested first to confirm the expected behaviour of each candidate expression. TBD - derived profiles + full test matrix

List of baseline profiles:

Profile | Expression
---|---
au-core-medicationrequest-ao-a|ao-a
au-core-medicationrequest-ao-b|ao-b
au-core-medicationrequest-ao-c|ao-c
au-core-medicationrequest-ao-d|ao-d
{:.grid}

**TBD**: derived profiles applying further constraints

List of baseline scenarios, to run against each profile:

Scenario| What it does
---|---
Precise value only | valid value accepted
Imprecise value only, no DAR | enforced precision requriement
DAR only | DAR accepted
Other extension only, no DAR | DAR required when value is not present
DAR + other extension, no value | DAR requirement satisfied
Precise value + DAR | should have one or the other 
Imprecise value + DAR | this shows issue with the xor expression - don't think AU Core wants to allow this
Precise value + other extension, no DAR | allowed, the invariant does not say anything about other extensions
Imprecise value + other extension, no DAR | enforced precision requriement
No authoredOn element | cardinality behavior
{:.grid}

#### Tests

The table below lists each test profile and its additional constraint, the test scenarios, expected validation results, and corresponding example instances.

<table border="1" cellspacing="0" cellpadding="0" width="100%">
    <thead>
        <tr>
            <th>Profile</th>
            <th>Constraint</th>
            <th>Test scenario</th>
            <th>Expected result</th>
            <th>Example id</th>
            </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="12"><a href="StructureDefinition-au-core-medicationrequest-ao-a.html">medicationrequest-ao-a</a></td>
            <td rowspan="12"><code>($this.hasValue() and $this.toString().length() >= 10) or ($this.hasValue().not() and extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists())</code></td>
            <td>authoredOn value present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-a-01.html">MedicationRequest-ao-a-01</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-a-02.html">MedicationRequest-ao-a-02</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, month only (7 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-a-03.html">MedicationRequest-ao-a-03</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, year only (4 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-a-04.html">MedicationRequest-ao-a-04</a></td>
        </tr>
        <tr>
            <td>authoredOn value not present, DAR used instead</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-a-05.html">MedicationRequest-ao-a-05</a></td>
        </tr>
        <tr>
            <td>Other extension only, no value and no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-a-06.html">MedicationRequest-ao-a-06</a></td>
        </tr>
        <tr>
            <td>DAR + other extension, no value</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-a-07.html">MedicationRequest-ao-a-07</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-a-08.html">MedicationRequest-ao-a-08</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-a-09.html">MedicationRequest-ao-a-09</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + other extension, no DAR </td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-a-10.html">MedicationRequest-ao-a-10</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + other extension, no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-a-11.html">MedicationRequest-ao-a-11</a></td>
        </tr>
        <tr>
            <td>No authoredOn element</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-a-12.html">MedicationRequest-ao-a-12</a></td>
        </tr>
        <tr>
            <td rowspan="12"><a href="StructureDefinition-au-core-medicationrequest-ao-b.html">medicationrequest-ao-b</a></td>
            <td rowspan="12"><code>($this.hasValue() implies $this.toString().length() >= 10) and ($this.hasValue().not() implies extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists())</code></td>
            <td>authoredOn value present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-b-01.html">MedicationRequest-ao-b-01</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-b-02.html">MedicationRequest-ao-b-02</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, month only (7 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-b-03.html">MedicationRequest-ao-b-03</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, year only (4 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-b-04.html">MedicationRequest-ao-b-04</a></td>
        </tr>
        <tr>
            <td>authoredOn value not present, DAR used instead</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-b-05.html">MedicationRequest-ao-b-05</a></td>
        </tr>
        <tr>
            <td>Other extension only, no value and no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-b-06.html">MedicationRequest-ao-b-06</a></td>
        </tr>
        <tr>
            <td>DAR + other extension, no value</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-b-07.html">MedicationRequest-ao-b-07</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-b-08.html">MedicationRequest-ao-b-08</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-b-09.html">MedicationRequest-ao-b-09</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + other extension, no DAR </td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-b-10.html">MedicationRequest-ao-b-10</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + other extension, no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-b-11.html">MedicationRequest-ao-b-11</a></td>
        </tr>
        <tr>
            <td>No authoredOn element</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-b-12.html">MedicationRequest-ao-b-12</a></td>
        </tr>
        <tr>
            <td rowspan="12"><a href="StructureDefinition-au-core-medicationrequest-ao-c.html">medicationrequest-ao-c</a></td>
            <td rowspan="12"><code>($this.hasValue() and $this.toString().length() >= 10) xor extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists()</code></td>
            <td>authoredOn value present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-c-01.html">MedicationRequest-ao-c-01</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-c-02.html">MedicationRequest-ao-c-02</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, month only (7 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-c-03.html">MedicationRequest-ao-c-03</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, year only (4 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-c-04.html">MedicationRequest-ao-c-04</a></td>
        </tr>
        <tr>
            <td>authoredOn value not present, DAR used instead</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-c-05.html">MedicationRequest-ao-c-05</a></td>
        </tr>
        <tr>
            <td>Other extension only, no value and no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-c-06.html">MedicationRequest-ao-c-06</a></td>
        </tr>
        <tr>
            <td>DAR + other extension, no value</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-c-07.html">MedicationRequest-ao-c-07</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-c-08.html">MedicationRequest-ao-c-08</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-c-09.html">MedicationRequest-ao-c-09</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + other extension, no DAR </td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-c-10.html">MedicationRequest-ao-c-10</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + other extension, no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-c-11.html">MedicationRequest-ao-c-11</a></td>
        </tr>
        <tr>
            <td>No authoredOn element</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-c-12.html">MedicationRequest-ao-c-12</a></td>
        </tr>
        <tr>
            <td rowspan="12"><a href="StructureDefinition-au-core-medicationrequest-ao-d.html">medicationrequest-ao-d</a></td>
            <td rowspan="12"><code>($this.hasValue() and $this.toString().length() >= 10) xor extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists()</code></td>
            <td>authoredOn value present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-d-01.html">MedicationRequest-ao-d-01</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-d-02.html">MedicationRequest-ao-d-02</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, month only (7 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-d-03.html">MedicationRequest-ao-d-03</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, year only (4 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-d-04.html">MedicationRequest-ao-d-04</a></td>
        </tr>
        <tr>
            <td>authoredOn value not present, DAR used instead</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-d-05.html">MedicationRequest-ao-d-05</a></td>
        </tr>
        <tr>
            <td>Other extension only, no value and no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-d-06.html">MedicationRequest-ao-d-06</a></td>
        </tr>
        <tr>
            <td>DAR + other extension, no value</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-d-07.html">MedicationRequest-ao-d-07</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-d-08.html">MedicationRequest-ao-d-08</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-d-09.html">MedicationRequest-ao-d-09</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + other extension, no DAR </td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-d-10.html">MedicationRequest-ao-d-10</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + other extension, no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-d-11.html">MedicationRequest-ao-d-11</a></td>
        </tr>
        <tr>
            <td>No authoredOn element</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-d-12.html">MedicationRequest-ao-d-12</a></td>
        </tr>
        <tr>
            <td rowspan="12"><a href="StructureDefinition-au-core-medicationrequest-ao-f.html">medicationrequest-ao-f</a></td>
            <td rowspan="12">Derives from medicationrequest-ao-d and requires authoredOn to have a value</td>
            <td>authoredOn value present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-f-01.html">MedicationRequest-ao-f-01</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-f-02.html">MedicationRequest-ao-f-02</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, month only (7 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-f-03.html">MedicationRequest-ao-f-03</a></td>
        </tr>
        <tr>
            <td>authoredOn value present, year only (4 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-f-04.html">MedicationRequest-ao-f-04</a></td>
        </tr>
        <tr>
            <td>authoredOn value not present, DAR used instead</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-f-05.html">MedicationRequest-ao-f-05</a></td>
        </tr>
        <tr>
            <td>Other extension only, no value and no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-f-06.html">MedicationRequest-ao-f-06</a></td>
        </tr>
        <tr>
            <td>DAR + other extension, no value</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-f-07.html">MedicationRequest-ao-f-07</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-f-08.html">MedicationRequest-ao-f-08</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-f-09.html">MedicationRequest-ao-f-09</a></td>
        </tr>
        <tr>
            <td>authoredOn precise value present + other extension, no DAR </td>
            <td>Pass</td>
            <td><a href="MedicationRequest-ao-f-10.html">MedicationRequest-ao-f-10</a></td>
        </tr>
        <tr>
            <td>authoredOn imprecise value present + other extension, no DAR</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-f-11.html">MedicationRequest-ao-f-11</a></td>
        </tr>
        <tr>
            <td>No authoredOn element</td>
            <td>Fail</td>
            <td><a href="MedicationRequest-ao-f-12.html">MedicationRequest-ao-f-12</a></td>
        </tr
    </tbody>
</table>