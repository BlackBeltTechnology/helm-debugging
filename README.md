# helm-debugging
debugging helm...
## Opened issue
https://github.com/helm/helm/issues/11611
## Values
Path: `mychart/values.yaml`
### A:
```
deployments:
  mysubchart1:
    enabled: false
  mysubchart2:
    enabled: true
```
### B:
```
mysubchart1:
  enabled: false
mysubchart2:
  enabled: true
```
## Chart
Path: `mychart/Chart.yaml`
### A:
```
dependencies:
  - name: mysubchart1
    repository: "file://charts/mysubchart1"
    version: 0.1.0
    # A
    condition: deployments.mysubchart1.enabled
    # B
    #condition: mysubchart1.enabled
  - name: mysubchart2
    repository: "file://charts/mysubchart2"
    version: 0.1.0
    # A
    condition: deployments.mysubchart2.enabled
    # B
    #condition: mysubchart2.enabled
```
### B:
```
dependencies:
  - name: mysubchart1
    repository: "file://charts/mysubchart1"
    version: 0.1.0
    # A
    #condition: deployments.mysubchart1.enabled
    # B
    condition: mysubchart1.enabled
  - name: mysubchart2
    repository: "file://charts/mysubchart2"
    version: 0.1.0
    # A
    #condition: deployments.mysubchart2.enabled
    # B
    condition: mysubchart2.enabled
```
## Commands
### C1:
```
helm upgrade --install helm-debugging mychart --dry-run --debug > output/helm-debug-$(date "+%Y%m%d-%H%M%S")
```
### C2:
```
helm upgrade --install helm-debugging mychart -f myfiles/extraconfig.yaml --dry-run --debug > output/helm-debug-$(date "+%Y%m%d-%H%M%S")
```
## Outputs
### A + C1 = `output-shared/helm-debug-20221207-131619`
```
COMPUTED VALUES:
deployments:
  mysubchart1:
    enabled: false
  mysubchart2:
    enabled: true
extra:
  data: []
  enable: ""
fruit:
  data: apple
  enabled: true
meal:
  drink: coffee
  food: goulash
menu: lunch
mysubchart2:
  dessert: muffin
  global: {}
  name: subchart-2
name: main-chart
```
### A + C2 = `output-shared/helm-debug-20221207-131625`
```
COMPUTED VALUES:
deployments:
  mysubchart1:
    enabled: false
  mysubchart2:
    enabled: true
extra:
  data:
  - pepper
  - salt
  - crushed red pepper
  enable: true
fruit:
  data: apple
  enabled: true
meal:
  drink: coffee
  food: goulash
menu: lunch
mysubchart1:
  dessert: pudding
mysubchart2:
  dessert: muffin
  global: {}
  name: subchart-2
name: main-chart
```
### B + C1 = `output-shared/helm-debug-20221207-131707`
```
COMPUTED VALUES:
extra:
  data: []
  enable: ""
fruit:
  data: apple
  enabled: true
meal:
  drink: coffee
  food: goulash
menu: lunch
mysubchart1:
  dessert: cake
  enabled: false
  global: {}
  name: subchart-1
mysubchart2:
  dessert: muffin
  enabled: true
  global: {}
  name: subchart-2
name: main-chart
```
### B + C2 = `output-shared/helm-debug-20221207-131710`
```
COMPUTED VALUES:
extra:
  data:
  - pepper
  - salt
  - crushed red pepper
  enable: true
fruit:
  data: apple
  enabled: true
meal:
  drink: coffee
  food: goulash
menu: lunch
mysubchart1:
  dessert: pudding
  enabled: false
mysubchart2:
  dessert: muffin
  enabled: true
  global: {}
  name: subchart-2
name: main-chart
```
## Notes
- https://helm.sh/docs/topics/charts/#importing-child-values-via-dependencies
- https://stackoverflow.com/questions/54032974/helm-conditionally-install-subchart
- https://helm.sh/docs/chart_best_practices/dependencies/#conditions-and-tags