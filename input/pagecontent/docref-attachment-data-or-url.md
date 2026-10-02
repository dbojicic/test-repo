### DocumentReference.content.attachment - URL or Data required

This test series tests the AU Core invariant requiring `DocumentReference.content.attachment.url` or `DocumentReference.content.attachment.data` to be present: `url.exists() or data.exists()`.
Related JIRA ticket: [FHIR-58786](https://jira.hl7.org/browse/FHIR-58786)

The table below lists the test scenarios, expected validation results, and corresponding example instances.

<table border="1" cellspacing="0" cellpadding="0" width="100%">
    <thead>
        <tr>
            <th>Profile</th>
            <th>Test scenario</th>
            <th>Expected result</th>
            <th>Example id</th>
            </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="4"><a href="StructureDefinition-au-core-documentreference-att.html">au-core-documentreference-att</a></td>
            <td>DocRef.content.attachhment.data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-01.html">DocumentReference-att-01</a></td>
        </tr>
        <tr>
            <td>DocRef.content.attachment.url present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-02.html">DocumentReference-att-02</a></td>
        </tr>
        <tr>
            <td>DocRef.content.attachment.url and data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-03.html">DocumentReference-att-03</a></td>
        </tr>
        <tr>
            <td>DocRef.content.attachment - no data or url</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-04.html">DocumentReference-att-04</a></td>
        </tr>
    </tbody>
</table>

### Extended testing
In addition to tests above.

Baseline profile: [au-core-documentreference-att](StructureDefinition-au-core-documentreference-att.html)

List of derived profiles:
- [au-core-documentreference-att-url](StructureDefinition-au-core-documentreference-att-url.html): derives from au-core-documentreference-att + requires url (1..1)
- [au-core-documentreference-att-data](StructureDefinition-au-core-documentreference-att-data.html): derives from au-core-documentreference-att + requires data (1..1)

List of baseline scenarios, to run against each profile:

Scenario|What it does
---|---
url present|Confirms a url with a value satisfies the rule
data present|Confirms a data with a value satisfies the rule
url and data present|Confirms having both is accepted
No url or data|Confirms the rule fails when both are absent
No url or data, contentType and title only|Confirms other Attachment elements cannot stand in for url or data
No url or data, extension on attachment only|Confirms an extension on the attachment itself cannot stand in for url or data
url DAR only, no value, no data|Shows the result when url has only DAR and no value. The url element exists, but contains no URL
data DAR only, no value, no url| Shows the result when data has only DAR and no value. The data element exists, but contains no URL
url other extension only, no value, no data| Shows the result when url has an extension that is not DAR and no value
url DAR only, no value + data present|Confirms a data value satisfies the rule even when url has no value
url value present + DAR|Shows the result when url has both a value and a Data Absent Reason extension. The rule does not say anything about this. 
Two content entries, one with url and one with data|Confirms the rule is checked separately for each content entry, and both pass
Two content entries, one with no url or data|Confirms the rule is checked separately for each content entry, and one fails
No content element|Confirms cardinality behaviour
content present, attachment absent| Confirms cardinality behaviour
{:.grid}

<table border="1" cellspacing="0" cellpadding="0" width="100%">
    <thead>
        <tr>
            <th>Profile</th>
            <th>Test scenario</th>
            <th>Expected result</th>
            <th>Example id</th>
            </tr>
    </thead>
    <tbody>
        <tr>
            <td rowspan="13"><a href="StructureDefinition-au-core-documentreference-att.html">au-core-documentreference-att</a></td>
            <td>url present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-01.html">DocumentReference-att-x-01</a></td>
        </tr>
        <tr>
            <td>data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-02.html">DocumentReference-att-x-02</a></td>
        </tr>
        <tr>
            <td>url and data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-03.html">DocumentReference-att-x-03</a></td>
        </tr>
        <tr>
            <td>no data or url</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-04.html">DocumentReference-att-x-04</a></td>
        </tr>
        <tr>
            <td>url DAR only, no value, no data</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-05.html">DocumentReference-att-x-05</a></td>
        </tr>
        <tr>
            <td>data DAR only, no value, no url</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-06.html">DocumentReference-att-x-06</a></td>
        </tr>
        <tr>
            <td>url other extension only, no value, no data</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-07.html">DocumentReference-att-x-07</a></td>
        </tr>
        <tr>
            <td>url DAR only, no value + data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-08.html">DocumentReference-att-08</a></td>
        </tr>
        <tr>
            <td>url value present + DAR</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-09.html">DocumentReference-att-x-09</a></td>
        </tr>
        <tr>
            <td>two content entries, one with url and one with data</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-10.html">DocumentReference-att-x-10</a></td>
        </tr>
        <tr>
            <td>two content entries, one with no url or data</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-11.html">DocumentReference-att-x-11</a></td>
        </tr>
        <tr>
            <td>no content element</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-12.html">DocumentReference-att-x-12</a></td>
        </tr>
        <tr>
            <td>content present, attachment absent</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-13.html">DocumentReference-att-x-13</a></td>
        </tr>
        <tr>
            <td rowspan="13"><a href="StructureDefinition-au-core-documentreference-att-url.html">au-core-documentreference-att-url</a></td>
            <td>url present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-url-01.html">DocumentReference-att-x-url-01</a></td>
        </tr>
        <tr>
            <td>data present</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-url-02.html">DocumentReference-att-x-url-02</a></td>
        </tr>
        <tr>
            <td>url and data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-url-03.html">DocumentReference-att-x-url-03</a></td>
        </tr>
        <tr>
            <td>no data or url</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-url-04.html">DocumentReference-att-x-url-04</a></td>
        </tr>
        <tr>
            <td>url DAR only, no value, no data</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-url-05.html">DocumentReference-att-x-url-05</a></td>
        </tr>
        <tr>
            <td>data DAR only, no value, no url</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-url-06.html">DocumentReference-att-x-url-06</a></td>
        </tr>
        <tr>
            <td>url other extension only, no value, no data</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-url-07.html">DocumentReference-att-x-url-07</a></td>
        </tr>
        <tr>
            <td>url DAR only, no value + data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-url-08.html">DocumentReference-att-x-url-08</a></td>
        </tr>
        <tr>
            <td>url value present + DAR</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-url-09.html">DocumentReference-att-x-url-09</a></td>
        </tr>
        <tr>
            <td>two content entries, one with url and one with data</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-url-10.html">DocumentReference-att-x-url-10</a></td>
        </tr>
        <tr>
            <td>two content entries, both with url and data</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-url-11.html">DocumentReference-att-x-url-11</a></td>
        </tr>
        <tr>
            <td>no content element</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-url-12.html">DocumentReference-att-x-url-12</a></td>
        </tr>
        <tr>
            <td>content present, attachment absent</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-url-13.html">DocumentReference-att-x-url-13</a></td>
        </tr>
        <tr>
            <td rowspan="13"><a href="StructureDefinition-au-core-documentreference-att-data.html">au-core-documentreference-att-data</a></td>
            <td>url present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-data-01.html">DocumentReference-att-x-data-01</a></td>
        </tr>
        <tr>
            <td>data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-data-02.html">DocumentReference-att-x-data-02</a></td>
        </tr>
        <tr>
            <td>url and data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-data-03.html">DocumentReference-att-x-data-03</a></td>
        </tr>
        <tr>
            <td>no data or url</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-data-04.html">DocumentReference-att-x-data-04</a></td>
        </tr>
        <tr>
            <td>url DAR only, no value, no data</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-data-05.html">DocumentReference-att-x-data-05</a></td>
        </tr>
        <tr>
            <td>data DAR only, no value, no url</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-data-06.html">DocumentReference-att-x-data-06</a></td>
        </tr>
        <tr>
            <td>url other extension only, no value, no data</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-data-07.html">DocumentReference-att-x-data-07</a></td>
        </tr>
        <tr>
            <td>url DAR only, no value + data present</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-data-08.html">DocumentReference-att-x-data-08</a></td>
        </tr>
        <tr>
            <td>data value present + DAR</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-data-09.html">DocumentReference-att-x-data-09</a></td>
        </tr>
        <tr>
            <td>two content entries, one with url and one with data</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-data-10.html">DocumentReference-att-x-data-10</a></td>
        </tr>
        <tr>
            <td>two content entries, both with url and data</td>
            <td>Pass</td>
            <td><a href="DocumentReference-att-x-data-11.html">DocumentReference-att-x-data-11</a></td>
        </tr>
        <tr>
            <td>no content element</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-data-12.html">DocumentReference-att-x-data-12</a></td>
        </tr>
        <tr>
            <td>content present, attachment absent</td>
            <td>Fail</td>
            <td><a href="DocumentReference-att-x-data-13.html">DocumentReference-att-x-data-13</a></td>
        </tr>
    </tbody>
</table>