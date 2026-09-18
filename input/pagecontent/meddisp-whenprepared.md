### MedicationDispense.whenPrepared precision

This test series tests the AU Core invariant requiring `MedicationDispense.whenPrepared`, when populated, to be precise to at least the day: 
`$this.hasValue() implies $this.toString().length() >= 10`

The tests use profiles derived from AU Core MedicationDispense with different downstream constraints on `MedicationDispense.whenPrepared`. The purpose is to confirm that the invariant constrains date precision without preventing downstream profiles from independently constraining the presence of `MedicationDispense.whenPrepared`, use of Data Absent Reason (DAR), or other valid profiling choices and without restricting any content valid content (other extensions, or DAR co-existing with a value).

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