## Github Rendering Issues

http://<IP of your SAP CAL backend\>:8443/.

Image_1_Surya

URL: Enter the URL of the on-premise system depending on type of your backend.
Pattern for the URL is in format: http://<virtual host name>:<virtual port name>/<OData service name>, note down the configured virtual host and virtual port in previous step 10 of configuring cloud connector.
For an SAP ECC system as backend: the name of the OData service for Business Partners is ZAPI_BUSINESS_PARTNER_SRV, then the URL would be for example http://sapsystem:5000/sap/opu/odata/sap/ZAPI_BUSINESS_PARTNER_SRV. Adjust the URL according to your configured virtual host name and port.
For an SAP S/4HANA system, the name of the OData service is API_BUSINESS_PARTNER, so the URL would be for example http://sapsystem:5000/sap/opu/odata/sap/API_BUSINESS_PARTNER. Adjust the URL according to your configured virtual host name and port.

