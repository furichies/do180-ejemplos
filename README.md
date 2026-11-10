# DO180 · Ejemplos para clase

Manifiestos YAML de apoyo al **guion de clase del DO180 4.22**. Se aplican directamente
sobre OpenShift, sin scripts, y usan las imágenes propias del curso cuando es posible.

Repositorio pensado para `wget` y `oc apply -f`:

```bash
oc apply -f https://raw.githubusercontent.com/furichies/do180-ejemplos/main/cap04/web.yaml
```

## Imágenes

| Imagen | Uso |
|---|---|
| `quay.io/ricardo_instructor_formacion/cooperativa-web:web-1.0` | aplicación web de diagnóstico (versión 1.0) |
| `quay.io/ricardo_instructor_formacion/cooperativa-web:web-1.1` | versión 1.1 para demos de actualización |
| `quay.io/ricardo_instructor_formacion/cooperativa-web:client-1.0` | caja de herramientas con `curl`, `nc`, `dig`, `jq` |
| `quay.io/ricardo_instructor_formacion/cooperativa-web:job-1.0` | Job que consulta o escala un Deployment con la API |
| `quay.io/ricardo_instructor_formacion/cooperativa-web:rootcheck-1.0` | comprueba el UID efectivo (SCC) |
| `registry.access.redhat.com/ubi9/ubi:latest` | pods genéricos de shell |

La aplicación `cooperativa-web` expone, entre otros:

```text
/            banner con versión, entorno, pod e IP
/whoami      JSON con pod, nodo, IP, versión y hits
/healthz     sonda de vida
/readyz      sonda de disponibilidad
/fail        marca la app como no lista
/recover     vuelve a marcarla como lista
/env         variables de entorno
/config      ficheros montados en /etc/cooperativa
/slow?ms=    latencia artificial
/load?cpu=   carga de CPU artificial
/downstream  llamada HTTP a otro servicio (host, port, path)
```

## Contenido

```text
cap02/inspect-deployment.yaml   Deployment para inspeccionar con oc
cap03/pod-shell.yaml            Pod UBI con shell
cap03/pod-web.yaml              Pod con la app propia
cap03/pod-broken-image.yaml     Pod con una etiqueta inexistente
cap03/pod-crashloop.yaml        App que arranca con un puerto inválido
cap03/pod-bad-command.yaml      Pod con un comando inexistente
cap04/web.yaml                  Deployment web + Service + Route + db + Service
cap04/client.yaml               Pod cliente con curl
cap04/job.yaml                  Job que prueba web y db
cap04/cronjob.yaml              CronJob cada minuto
cap05/config.yaml               ConfigMaps y Secret
cap05/web-config.yaml           Deployment con envFrom y volúmenes + Service/Route
cap05/pvc.yaml                  PVC con la clase por defecto
cap05/pod-pvc.yaml              Pod que escribe en el PVC
cap05/statefulset.yaml          Headless Service + StatefulSet con PVC por réplica
cap06/pod-restart.yaml          restartPolicy Always vs Never
cap06/web-probes.yaml           Deployment con startup/readiness/liveness + Service/Route
cap06/hpa.yaml                  HPA por CPU
cap07/web-v1.yaml               Deployment web-1.0 + Service/Route
cap07/imagestream.yaml          ImageStream web:1.0
cap07/deployment-ist.yaml       Deployment que consume la ImageStream
cap08/capstone.yaml             Ejercicio final completo
cap08/broken.yaml               Ejercicio de troubleshooting
```

## Uso rápido

```bash
# Login en el CRC
oc login -u developer -p developer https://api.crc.testing:6443 --insecure-skip-tls-verify=true

# Proyecto y ejemplo
oc new-project do180-c4
oc apply -f https://raw.githubusercontent.com/furichies/do180-ejemplos/main/cap04/web.yaml
oc get deploy,svc,route
```

Para ver la aplicación:

```bash
HOST=$(oc get route web -o jsonpath='{.spec.host}')
curl -s "http://$HOST/"
```

Si el DNS de `*.apps-crc.testing` no resuelve desde tu equipo:

```bash
curl -s --resolve "$HOST:80:172.16.1.200" "http://$HOST/"
```

## Convenciones

- Los manifiestos **no** incluyen `namespace`: se aplican con `-n` o tras `oc project`.
- Las `Route` no fijan `host`: toman el dominio del ingress.
- Los nombres de proyecto recomendados están en el guion: `do180-c1`, `do180-c2`, …
- El capítulo 7 usa el proyecto `do180-c7` porque la referencia interna del registro incluye
  el nombre del proyecto.
