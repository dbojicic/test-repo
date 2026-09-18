### MedicationRequest.authoredOn date or Data Absent Reason

This test series evaluates three candidate FHIRPath expressions for the AU Core `MedicationRequest.authoredOn` invariant **au-core-medreq-01**, raised in [FHIR-59045](https://jira.hl7.org/browse/FHIR-59045).

The human readable invariant description is:
> Date shall be precise to the day or, if not available, the Data Absent Reason extension shall be present

The tests evaluate whether each candidate expression:
- requires a populated `MedicationRequest.authoredOn` value to be precise to at least the day
- requires Data Absent Reason (DAR) when `MedicationRequest.authoredOn` has no value
- determines how each expression behaves when a value and DAR are both present
- allows further constraints to be applied to `MedicationRequest.authoredOn` in downstream profiles

This will be used to determine which expression correctly implements the human readable description without intentionally restricting downstream use and profiling choices around `MedicationRequest.authoredOn` and the use of DAR.

Question: When a sufficiently precise value is present, does AU Core also require DAR to be absent?

#### Candidate expressions

The following candidate expressions are tested:

Series code|Candidate expression
--- | --- 
ao-or|`($this.hasValue() and $this.toString().length() >= 10) or ($this.hasValue().not() and extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists())`
ao-imp|`($this.hasValue() implies $this.toString().length() >= 10) and ($this.hasValue().not() implies extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists())`
ao-xor|`($this.hasValue() and $this.toString().length() >= 10) xor extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists()`
ao-imp-excl|`($this.hasValue() implies ($this.toString().length() >= 10 and extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists().not())) and ($this.hasValue().not() implies extension('http://hl7.org/fhir/StructureDefinition/data-absent-reason').exists())`
{:.grid}

The purpose of this test series is to confirm which expression(s) correctly implement the human readable description, without unintentionally restricting downstream profiling choices around authoredOn cardinality or use of DAR. 
This will provide input into discussion about which of the correct expressions is preferable for AU Core.

#### Initial expression comparison

A FHIR primitive element can contain both a primitive value and extensions. The presence of a value and DAR on `MedicationRequest.authoredOn` should be considered when comparing the candidate expressions. 
**Question**: When a sufficiently precise value is present, does AU Core also require DAR to be absent?

The following table shows the expected result for each expression based on its FHIRPath logic:

Scenario | ao-or | ao-imp | ao-xor | ao-imp-excl
--- | --- | --- | ---
Precise value only|Pass|Pass|Pass|Pass
Imprecise value only|Fail|Fail|Fail|Fail
DAR only|Pass|Pass|Pass|Pass
Other extension only, no DAR|Fail|Fail|Fail|Fail
Precise value + DAR|Pass|Pass|Fail|Fail
Imprecise value + DAR|Fail|Fail|**Pass**|Fail
{:.grid}

The differences result from the logic of each expression:

- **ao-or** requires either a sufficiently precise value, or no value with DAR present. When a sufficiently precise value is present, the expression passes regardless of whether DAR is also present.
- **ao-imp** requires a value, when present, to be sufficiently precise, and requires DAR when there is no value. No additional restriction on DAR when a value is present. Its expected results are the same as ar-or.
- **ao-xor** requires exactly one side of the expression to evaluate to true. It fails a precise value with DAR. However, an imprecise value makes the first condition false; when DAR is also present, the second condition is true and the overall expression passes. 
- **ao-imp-excl** requires a value, when present, to be sufficiently precise and DAR to be absent. When there is no value, it requires DAR to be present. It therefore fails both a precise value with DAR and an imprecise value with DAR.

These scenarios are tested first to confirm the expected behaviour of each candidate expression before applying the full test matrix.

List of baseline profiles:

Profile | Expression
---|---
au-core-medicationrequest-ao-or|ao-or
au-core-medicationrequest-ao-imp|ao-imp
au-core-medicationrequest-ao-xor|ao-xor
au-core-medicationrequest-ao-imp-excl|ao-imp-excl
{:.grid}

**TBD**: derived profiles applying further constraints

Baseline scenarios, to run against each profile:
Scenario|
---|---
Precise value only | valid value accepted
Imprecise value only, no DAR | enforced precision requriement
DAR only | DAR accepted
Other extension only, no DAR | DAR required when value is not present
DAR + other extension, no value | DAR requirement satisfied
Precise value + DAR | allowed or not?? What do we want??
Imprecise value + DAR | this shows issue with the xor expression - don't think AU Core wants to allow this
Precise value + other extension, no DAR | allowed, the invariant does not say anything about other extensions
Imprecise value + other extension, no DAR | enforced precision requriement
No authoredOn element | cardinality behavior




The table below lists each test profile and its additional constraint, the test scenarios, expected validation results, and corresponding example instances.

<table border="1" cellspacing="0" cellpadding="0" width="100%">
    <thead>
        <tr>
            <th>Profile</th>
            <th>Additional constraint</th>
            <th>Test scenario</th>
            <th>Expected result</th>
            <th>Example id</th>
            </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="10"><a href="StructureDefinition-au-core-medicationdispense-wp-a.html">au-core-medicationdispense-wp-a</a></td>
            <td rowspan="10">Derives from AU Core MedicationDispense with no additional constraints</td>
            <td>whenPrepared not present</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-a-01.html">medicationdispense-wp-a-01</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-a-02.html">medicationdispense-wp-a-02</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-a-03.html">medicationdispense-wp-a-03</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, month only (7 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationDispense-wp-a-04.html">medicationdispense-wp-a-04</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, year only (4 chars)</td>
            <td>Fail</td>
            <td><a href="MedicationDispense-wp-a-05.html">medicationdispense-wp-a-05</a></td>
        </tr>
        <tr>
            <td>whenPrepared value not present, DAR used instead</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-a-06.html">medicationdispense-wp-a-06</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR together</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-a-07.html">medicationdispense-wp-a-07</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + unrelated extension</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-a-08.html">medicationdispense-wp-a-08</a></td>
        </tr>
        <tr>
           <td>whenPrepared value not present, unrelated extension only</td>
            <td>Pass</td>
             <td><a href="MedicationDispense-wp-a-09.html">medicationdispense-wp-a-09</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR + unrelated extension</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-a-10.html">medicationdispense-wp-a-10</a></td>
        </tr>
        <tr>
            <td rowspan="10"><a href="StructureDefinition-au-core-medicationdispense-wp-b.html">au-core-medicationdispense-wp-b</a></td>
            <td rowspan="10">whenPrepared is mandatory and is required to have a value</td>
            <td>whenPrepared not present</td>
            <td>Fail - cardinality (1..1)</td>
            <td><a href="MedicationDispense-wp-b-01.html">medicationdispense-wp-b-01</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-b-02.html">medicationdispense-wp-b-02</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-b-03.html">medicationdispense-wp-b-03</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, month only (7 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-b-04.html">medicationdispense-wp-b-04</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, year only (4 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-b-05.html">medicationdispense-wp-b-05</a></td>
        </tr>
        <tr>
            <td>whenPrepared value not present, DAR used instead</td>
            <td>Fail - DAR not accepted as the value sub</td>
            <td><a href="MedicationDispense-wp-b-06.html">medicationdispense-wp-b-06</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR together</td>
            <td>Pass - DAR extension not permitted</td>
            <td><a href="MedicationDispense-wp-b-07.html">medicationdispense-wp-b-07</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + unrelated extension</td>
            <td>Pass - unrelated extension not restricted</td>
            <td><a href="MedicationDispense-wp-b-08.html">medicationdispense-wp-b-08</a></td>
        </tr>
        <tr>
            <td>whenPrepared value not present, unrelated extension only</td>
            <td>Fail - no value present</td>
            <td><a href="MedicationDispense-wp-b-09.html">medicationdispense-wp-b-09</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR + unrelated extension</td>
            <td>Fail - DAR extension still not permitted, independent of the other extension</td>
            <td><a href="MedicationDispense-wp-b-10.html">medicationdispense-wp-b-10</a></td>
        </tr>
        <tr>
            <td rowspan="10"><a href="StructureDefinition-au-core-medicationdispense-wp-c.html">au-core-medicationdispense-wp-c</a></td>
            <td rowspan="10">whenPrepared is mandatory; DAR is permitted</td>
             <td>whenPrepared value not present, no DAR</td>
            <td>Fail - mandatory requirement (neither value nor DAR present)</td>
            <td><a href="MedicationDispense-wp-c-01.html">medicationdispense-wp-c-01</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-c-02.html">medicationdispense-wp-c-02</a></td>
          </tr>
        <tr>
            <td>whenPrepared present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-c-03.html">medicationdispense-wp-c-03</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, month only (7 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-c-04.html">medicationdispense-wp-c-04</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, year only (4 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-c-05.html">medicationdispense-wp-c-05</a></td>
        </tr>
        <tr>
            <td>whenPrepared value not present, DAR used instead</td>
            <td>Pass - DAR satisfies mandatory requirement</td>
            <td><a href="MedicationDispense-wp-c-06.html">medicationdispense-wp-c-06</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR together</td>
            <td>Pass - DAR permitted; co-existing with a value not prohibited</td>
            <td><a href="MedicationDispense-wp-c-07.html">medicationdispense-wp-c-07</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + unrelated extension</td>
            <td>Pass - unrelated extension unaffected</td>
            <td><a href="MedicationDispense-wp-c-08.html">medicationdispense-wp-c-08</a></td>
        </tr>
        <tr>
            <td>whenPrepared value not present, unrelated extension only</td>
            <td>Pass - 1..1 cardinality from wp-c is satisfied by any extension present, not just DAR</td>
            <td><a href="MedicationDispense-wp-c-09.html">medicationdispense-wp-c-09</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR + unrelated extension</td>
            <td>Pass - all three coexist without conflict</td>
            <td><a href="MedicationDispense-wp-c-10.html">medicationdispense-wp-c-10</a></td>
        </tr>
        <tr>
            <td rowspan="10"><a href="StructureDefinition-au-core-medicationdispense-wp-d.html">au-core-medicationdispense-wp-d</a></td>
            <td rowspan="10">whenPrepared is mandatory via a separate invariant requiring either a value or DAR</td>
            <td>whenPrepared value not present, no DAR</td>
            <td>Fail - value or DAR invariant</td>
            <td><a href="MedicationDispense-wp-d-01.html">medicationdispense-wp-d-01</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-d-02.html">medicationdispense-wp-d-02</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-d-03.html">medicationdispense-wp-d-03</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, month only (7 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-d-04.html">medicationdispense-wp-d-04</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, year only (4 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-d-05.html">medicationdispense-wp-d-05</a></td>
        </tr>
        <tr>   
            <td>whenPrepared value not present, DAR used instead</td>
            <td>Pass - DAR satisfies value-or-DAR invariant</td>
            <td><a href="MedicationDispense-wp-d-06.html">medicationdispense-wp-d-06</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR together</td>
            <td>Pass - DAR permitted; co-existing with a value not prohibited</td>
            <td><a href="MedicationDispense-wp-c-07.html">medicationdispense-wp-c-07</a></td></tr>
        <tr>
            <td>whenPrepared value + unrelated extension</td>
            <td>Pass - unrelated extension unaffected</td>
            <td><a href="MedicationDispense-wp-c-08.html">medicationdispense-wp-c-08</a></td>
        </tr>
        <tr>
            <td>whenPrepared value not present, unrelated extension only</td>
            <td>Fail - neither value nor DAR present</td>
            <td><a href="MedicationDispense-wp-c-09.html">medicationdispense-wp-c-09</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR + unrelated extension</td>
            <td>Pass - all three coexist without conflict</td>
            <td><a href="MedicationDispense-wp-c-10.html">medicationdispense-wp-c-10</a></td>
        </tr>
        <tr>
            <td rowspan="10"><a href="StructureDefinition-au-core-medicationdispense-wp-e.html">au-core-medicationdispense-wp-e</a></td>
            <td rowspan="10">whenPrepared remains optional; a separate invariant prohibits DAR</td>
            <td>whenPrepared value not present, no DAR</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-e-01.html">medicationdispense-wp-e-01</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-e-02.html">medicationdispense-wp-e-02</a></td>
        </tr>
        <tr>    
            <td>whenPrepared present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-e-03.html">medicationdispense-wp-e-03</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, month only (7 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-e-04.html">medicationdispense-wp-e-04</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, year only (4 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-e-05.html">medicationdispense-wp-e-05</a></td>
        </tr>
        <tr>
            <td>whenPrepared value not present, DAR used instead</td>
            <td>Fail - DAR prohibition invariant fires</td>
            <td><a href="MedicationDispense-wp-e-06.html">medicationdispense-wp-e-06</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR together</td>
            <td>Fail - DAR prohibition invariant fires</td>
            <td><a href="MedicationDispense-wp-e-07.html">medicationdispense-wp-e-07</a></td></tr>
        <tr>
            <td>whenPrepared value + unrelated extension</td>
            <td>Pass - unrelated extension unaffected</td>
            <td><a href="MedicationDispense-wp-e-08.html">medicationdispense-wp-e-08</a></td>
        </tr>
        <tr>
            <td>whenPrepared value not present, unrelated extension only</td>
            <td>Pass - element optional; extension present is not DAR so prohibition invariant doesn't fire</td>
            <td><a href="MedicationDispense-wp-e-09.html">medicationdispense-wp-e-09</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR + unrelated extension</td>
            <td>Fail - DAR prohibition invariant fires; unrelated extension irrelevant</td>
            <td><a href="MedicationDispense-wp-e-10.html">medicationdispense-wp-c-10</a></td>
        </tr>
        <tr>
            <td rowspan="10"><a href="StructureDefinition-au-core-medicationdispense-wp-f.html">au-core-medicationdispense-wp-f</a></td>
            <td rowspan="10">Derives from profile C with no additional constraints on whenPrepared, to test inheritance through a second level of derivation</td>
            <td>whenPrepared not present, no DAR</td>
            <td>Fail - mandatory requirement (neither value nor DAR present)</td>
            <td><a href="MedicationDispense-wp-f-01.html">medicationdispense-wp-f-01</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, full datetime</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-f-02.html">medicationdispense-wp-f-02</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, date only (10 chars)</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-f-03.html">medicationdispense-wp-f-03</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, month only (7 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-f-04.html">medicationdispense-wp-f-04</a></td>
        </tr>
        <tr>
            <td>whenPrepared present, year only (4 chars)</td>
            <td>Fail - whenPrepared precision invariant</td>
            <td><a href="MedicationDispense-wp-f-05.html">medicationdispense-wp-f-05</a></td>
        </tr>
        <tr>
            <td>whenPrepared not present, DAR used instead</td>
            <td>Pass - DAR satisfies inherited mandatory requirement</td>
            <td><a href="MedicationDispense-wp-f-06.html">medicationdispense-wp-f-06</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR together</td>
            <td>Pass - DAR permitted; co-existing with a value not prohibited</td>
            <td><a href="MedicationDispense-wp-e-07.html">medicationdispense-wp-f-07</a></td></tr>
        <tr>
            <td>whenPrepared value + unrelated extension</td>
            <td>Pass - unrelated extension unaffected</td>
            <td><a href="MedicationDispense-wp-e-08.html">medicationdispense-wp-f-08</a></td>
        </tr>
        <tr>
            <td>whenPrepared value not present, unrelated extension only</td>
            <td>Pass - 1..1 cardinality from wp-c is satisfied by any extension present, not just DAR</td>
            <td><a href="MedicationDispense-wp-e-09.html">medicationdispense-wp-f-09</a></td>
        </tr>
        <tr>
            <td>whenPrepared value + DAR + unrelated extension</td>
            <td>Pass</td>
            <td><a href="MedicationDispense-wp-e-10.html">medicationdispense-wp-f-10</a></td>
        </tr>
    </tbody>
</table>