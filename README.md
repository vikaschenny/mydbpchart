Sure, I'll help you create a Helm chart for the provided Kubernetes YAML. We'll create three separate Helm templates for ConfigMap, Deployment, and Service.

1. Create a new Helm chart by running:

```bash
helm create mydbpchart
```

2. Replace the content of the generated `templates` folder with the following:

`templates/configmap.yaml`:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ include "mydbpchart.fullname" . }}
  namespace: {{ .Values.namespace }}
data:
  CONSUMER_BROKER_ADDRESS: >-
    {{ .Values.consumerBrokerAddress }}
  CONSUMER_SASL_JAAS_CONFIG: '{{ .Values.consumerSaslJaasConfig }}'
  CONSUMER_SASL_MECHANISM: {{ .Values.consumerSaslMechanism }}
  CONSUMER_SECURITY_PROTOCOL: '{{ .Values.consumerSecurityProtocol }}'
  DEAD_LETTER_TOPIC: {{ .Values.deadLetterTopic }}
  FAIL_APPLICATION_ON_ERROR: '{{ .Values.failApplicationOnError }}'
  GOVERNANCE_URL: '{{ .Values.governanceUrl }}'
  INPUT_TOPIC: {{ .Values.inputTopic }}
  KAFKA_GROUP_ID: {{ .Values.kafkaGroupId }}
  KAFKA_KEY_DESERIALIZER: {{ .Values.kafkaKeyDeserializer }}
  KAFKA_VALUE_DESERIALIZER: {{ .Values.kafkaValueDeserializer }}
  LOGGING_LEVEL: {{ .Values.loggingLevel }}
  OUTPUT_TOPIC: {{ .Values.outputTopic }}
  PRODUCER_BROKER_ADDRESS: >-
    {{ .Values.producerBrokerAddress }}
  TRANSFORM_CLASS_NAME: {{ .Values.transformClassName }}
  SERVICE_PROPS_TABLE_NAME: {{ .Values.servicePropsTableName }}
  SERVICE_PROPS_TYPE: {{ .Values.servicePropsType }}
  SERVICE_PROPS_VALUE_COL: {{ .Values.servicePropsValueCol }}
  SERVICE_PROPS_HOSTS: {{ .Values.servicePropsHosts }}
  SERVICE_PROPS_KEY_COL: {{ .Values.servicePropsKeyCol }}
  SERVICE_PROPS_KEYSPACE_NAME: {{ .Values.servicePropsKeyspaceName }}
  SERVICE_PROPS_USERNAME: {{ .Values.servicePropsUsername }}
  SERVICE_PROPS_PASSWORD: {{ .Values.servicePropsPassword }}
  SERVICE_PROPS_ENABLED_DEVICES: {{ .Values.servicePropsEnabledDevices }}
```

`templates/deployment.yaml`:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: {{ include "mydbpchart.fullname" . }}
  namespace: {{ .Values.namespace }}
spec:
  replicas: {{ .Values.replicas }}
  selector:
    matchLabels:
      {{- include "mydbpchart.selectorLabels" . | nindent 4 }}
  template:
    metadata:
      labels:
        {{- include "mydbpchart.selectorLabels" . | nindent 8 }}
      annotations:
        prometheus.io/path: /actuator/prometheus
        prometheus.io/port: '8081'
        prometheus.io/scrape: 'true'
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: >-
            {{ .Values.image.repository }}/{{ .Values.image.name }}:{{ .Values.image.tag }}
          ports:
            - containerPort: 8081
              protocol: TCP
          envFrom:
            - configMapRef:
                name: {{ include "mydbpchart.fullname" . }}
          resources: {{ .Values.resources | toYaml | nindent 12 }}
          terminationMessagePath: /dev/termination-log
          terminationMessagePolicy: File
          imagePullPolicy: {{ .Values.image.pullPolicy }}
      restartPolicy: {{ .Values.restartPolicy }}
      terminationGracePeriodSeconds: {{ .Values.terminationGracePeriodSeconds }}
```

`templates/service.yaml`:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ include "mydbpchart.fullname" . }}
  namespace: {{ .Values.namespace }}
spec:
  ports:
    - protocol: TCP
      port: {{ .Values.servicePort }}
      targetPort: {{ .Values.serviceTargetPort }}
      nodePort: {{ .Values.serviceNodePort }}
  selector:
    app: {{ template "mydbpchart.fullname" . }}
  type: LoadBalancer
```

3. Update `values.yaml` with your desired default values:

```yaml
# values.yaml
image:
  repository: 
  name: 
  tag: test
  pullPolicy: Always

namespace: 

consumerBrokerAddress: 
consumerSaslJaasConfig: ''
consumerSaslMechanism: PLAIN
consumerSecurityProtocol: ''
deadLetterTopic:
failApplicationOnError: 'true'
governanceUrl: 
inputTopic: 
kafkaGroupId: dc.unification.test
kafkaKeyDeserializer: org.apache.kafka.common.serialization.BytesDeserializer
kafkaValueDeserializer: org.apache.kafka.common.serialization.BytesDeserializer
loggingLevel: DEBUG
outputTopic: 
producerBrokerAddress: >-
transformClassName: 
servicePropsTableName: intelliset_store
servicePropsType: cassandra
servicePropsValueCol: val
servicePropsHosts: 
servicePropsKeyCol: key
servicePropsKeyspaceName: qaop
servicePropsUsername: ''
servicePropsPassword: ''
servicePropsEnabledDevices:

replicas: 1
selectorLabels:
  bb: dbp-dc-unification

resources: {}

terminationGracePeriodSeconds: 30

servicePort:
serviceTargetPort:
serviceNodePort: 
```

Now, you have a Helm chart with three templates corresponding to ConfigMap, Deployment, and Service. Customize the `values.yaml` file according to your needs.
