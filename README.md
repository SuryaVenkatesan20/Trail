
- Update `incident-management-srv-api` in `incident-management-destination-service`

    - Add `HTML5.IASDependencyName: incidents-api`
    
      ```yaml hl_lines="5"
          - Authentication: NoAuthentication
            HTML5.IASDependencyName: incidents-api
            Name: incidents-management-srv-api
            ProxyType: Internet
            Type: HTTP
            URL: ~{srv-api/srv-url}
          existing_destinations_policy: update
      ```
  - Update your `build-parameters ` with the following code:
      ```yaml
        build-parameters:
          before-all:
          - builder: custom
            commands:
            - npx -p @sap/cds-dk cds build --production
            - npx -p cpy-cli -- cpy "ams" "./gen/srv"
      ```
2. Update `app/incidents/xs-app.json` with the following code:
