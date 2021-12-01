## Welcome to GitHub Pages

You can use the [editor on GitHub](https://github.com/wirwolf/test/edit/gh-pages/index.md) to maintain and preview the content for your website in Markdown files.

Whenever you commit to this repository, GitHub Pages will run [Jekyll](https://jekyllrb.com/) to rebuild the pages in your site, from the content in your Markdown files.

### Markdown

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/wirwolf/test/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.

```
{{ if (eq $.Values.horizontalPodAutoscaler.enabled true) }}
apiVersion: monitoring.coreos.com/v1
kind: PrometheusRule
metadata:
  name: {{ $.Release.Name }}
spec:
  groups:
    - name: {{ $.Release.Name }}-queue-size # Define the name of your rule
      rules:
        - record: {{ include "workers.hpa_metric_name" . }}_messages_waiting_in_queue # Уникальное имя метрики.
          expr: rabbitmq_queue_messages{queue="{{ $.Values.horizontalPodAutoscaler.rabbitmq.queue }}", vhost="{{ $.Values.horizontalPodAutoscaler.rabbitmq.vhost }}"} # Запрос в Prometheus c фильтрацией 
---
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: {{ $.Release.Name }}
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: {{ $.Release.Name }}
  minReplicas: {{ $.Values.horizontalPodAutoscaler.minReplicas }}
  maxReplicas: {{ $.Values.horizontalPodAutoscaler.maxReplicas }}
  metrics:
    - type: Object
      object:
        metric:
          name: {{ include "workers.hpa_metric_name" . }}_messages_waiting_in_queue
        describedObject:
          apiVersion: v1
          kind: Namespace
          name: {{ $.Release.Namespace }}
        target:
          type: Value
          value: {{ $.Values.horizontalPodAutoscaler.rabbitmq.value }}

{{- end }}
```

