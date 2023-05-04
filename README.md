# Prueba de configuración de dominio de seguridad con Jboss EAP 7.2.

- Se creará un usuario rxcUser, con contraseña rxcPass y rol rxcRole.
- Se securizará la ruta /private con el rol rxcRole.

## Configuración de JBOSS

#### Añadir un realm filesystem-realm.

```
/subsystem=elytron/filesystem-realm=rxcFsRealm:add(path=rxc-fs-realm-users,relative-to=jboss.server.config.dir)
{"outcome" => "success"}
```

Si el sistema pide reload, hay que recargar la configuración de los nodos, desde la consola de Jboss, tab Runtime. Sino no quedarán añadidos el resto de recursos...

```
/subsystem=elytron/filesystem-realm=rxcFsRealm:add-identity(identity=rxcUser)
{"outcome" => "success"}
/subsystem=elytron/filesystem-realm=rxcFsRealm:set-password(identity=rxcUser, clear={password="rxcPass"})
{"outcome" => "success"}
/subsystem=elytron/filesystem-realm=rxcFsRealm:add-identity-attribute(identity=rxcUser, name=Roles, value=["rxcRole"])
{"outcome" => "success"}
```

```
/subsystem=elytron/simple-role-decoder=from-roles-attribute:add(attribute=Roles)
{"outcome" => "success"}
```

```
/subsystem=elytron/security-domain=rxcFsDomain:add(realms=[{realm=rxcFsRealm,role-decoder=from-roles-attribute}],default-realm=rxcFsRealm,permission-mapper=default-permission-mapper)
{"outcome" => "success"}
```

```
/subsystem=undertow/application-security-domain=rxcDomain:add(security-domain=rxcFsDomain)
{"outcome" => "success"}
```

## Enlaces

[Configurar Elyctron](https://access.redhat.com/documentation/es-es/red_hat_jboss_enterprise_application_platform/7.2/html/how_to_configure_identity_management/elytron_secure_apps)

[Configurar la aplicación](https://access.redhat.com/documentation/es-es/red_hat_jboss_enterprise_application_platform/7.2/html/how_to_configure_identity_management/application_configuration#configure-app-authentication)
