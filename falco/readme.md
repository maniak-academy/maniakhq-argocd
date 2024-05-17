


helm install falco -n falco \
    --set driver.kind=modern_ebpf \
    --set falcosidekick.enabled=true \
    --set falcosidekick.webui.enabled=true \
    --set auditLog.enabled=true \
    --set falco.jsonOutput=true \
    --set falco.fileOutput.enabled=true \
    --set falcosidekick.config.slack.webhookurl=$(base64 --decode <<< "aHR0cHM6Ly9ob29rcy5zbGFjay5jb20vc2VydmljZXMvVDA0UURRNk44MlYvQjA3MzFSMTAyUk0vMWZXM2R2Mk9uM0REMk5EVmxvTExEdjdY") \
    --set falcosidekick.config.slack.minimumpriority=notice \
    --set falcosidekick.config.customfields="user:smaniak" \
    --set tty=true \
     falcosecurity/falco


username admin/admin