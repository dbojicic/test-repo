### DocumentReference.content.attachment - URL or Data required

This test series tests the AU Core invariant requiring `DocumentReference.content.attachment.url` or `DocumentReference.content.attachment.data` to be present: `url.exists() or data.exists()`

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