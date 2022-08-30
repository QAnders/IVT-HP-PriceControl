# Entsoe API

## Register account
Register an account with https://transparency.entsoe.eu

Once you have gotten the confirmation e-mail and confirmed the same, login and go to your settings and set your country.

After that, send an e-mail (from the same account) to transparency@entsoe.eu with the Subject as "Restful API access" and the registered e-mail in the body.
See: https://transparency.entsoe.eu/content/static_content/Static%20content/web%20api/Guide.html#_authentication_and_authorisation

## Usage
Once you gotten the API access and have generated your API key (SECURITY_TOKEN) it's the "Day ahead price" information we are interested in, e.g.:

    documentType: A44 = Price Document
    in_Domain & out_Domain: 10Y1001A1001A46L (Sweden SE1: 10Y1001A1001A44P, SE2: 10Y1001A1001A45N, SE3: 10Y1001A1001A46L, SE4: 10Y1001A1001A47J)
    periodStart: date/time-stamp from (as 202208020000 for 2022-08-02 00:00)
    periodEnd:  date/time-stamp to (as 202208022300 for 2022-08-02 23:00)

`GET https://web-api.tp.entsoe.eu/api?securityToken={your-secret-key}&documentType=A44&in_Domain=10Y1001A1001A46L&out_Domain=10Y1001A1001A46L&periodStart=202208020000&periodEnd=202208022300`

If you want to use a POST request:

    POST https://web-api.tp.entsoe.eu/api
    HEADERS:
    content-type: application/xml
    security_token: {your-secret-key}

BODY:

    <StatusRequest_MarketDocument xmlns="urn:iec62325.351:tc57wg16:451-5:statusrequestdocument:4:0">
        <mRID>{U-U-I-D}</mRID> <!-- Generate a unique ID, e.g. a UUID -->
        <type>A59</type> <!-- Hardcoded -->
        <sender_MarketParticipant.mRID codingScheme="A01">10X1001A1001A450</sender_MarketParticipant.mRID> <!-- Hardcoded -->
        <sender_MarketParticipant.marketRole.type>A07</sender_MarketParticipant.marketRole.type> <!-- Harcoded -->
        <receiver_MarketParticipant.mRID codingScheme="A01">10X1001A1001A450</receiver_MarketParticipant.mRID> <!-- Hardcoded -->
        <receiver_MarketParticipant.marketRole.type>A32</receiver_MarketParticipant.marketRole.type> <!-- Harcoded -->
        <createdDateTime>2022-08-02T13:00:00Z</createdDateTime> <!-- Date/time-stamp of the generation of the request -->
        <AttributeInstanceComponent>
            <attribute>DocumentType</attribute> <!-- Hardcoded -->
            <attributeValue>A44</attributeValue> <!-- Hardcoded, means Day Ahead Price -->
        </AttributeInstanceComponent>
        <AttributeInstanceComponent>
            <attribute>In_Domain</attribute> <!-- Hardcoded -->
            <attributeValue>10Y1001A1001A46L</attributeValue> <!-- The market for the price, Sweden SE3 = 10Y1001A1001A46L -->
        </AttributeInstanceComponent>
        <AttributeInstanceComponent>
            <attribute>Out_Domain</attribute> <!-- Hardcoded-->
            <attributeValue>10Y1001A1001A46L</attributeValue> <!-- Same as In_Domain -->
        </AttributeInstanceComponent>
        <AttributeInstanceComponent>
            <attribute>TimeInterval</attribute> <!-- Hardcoded-->
            <attributeValue>2022-08-03T00:00Z/2022-08-03T23:00Z</attributeValue> <!-- THe period from/to, to get a full day add from 00:00 to 23:00 -->
        </AttributeInstanceComponent>
    </StatusRequest_MarketDocument>

The response will contain "Point/position" from 1 to 24 which correlates to the hours of the day, meaning position 1 is 00:00-01:00, and so on.

The response is in EUR/MWh so to convert it to SEK öre/kWh take the price in EUR/MWh (e.g. 11.75) times the currency exchange rate for SEK/EUR (e.g. 10.36) and then we need to divide it with 1000 to get kWh from MWh and again take it times 100 to get öre insted of SEK.
We can shorten the MWh->kWh and öre->SEK by instead divide the result by ten (as divided by 1000, times 100 equals out to divided by 10), so e.g. 11.75 EUR/MWh * 10.36 / 10 = 12.173 öre/kWh.


