Step-by-Step Guide to Setting Up Prometheus Monitoring with Blackbox Exporter and Alertmanager
==============================================================================================

1\. Install Prometheus, Blackbox Exporter, and Alertmanager
-----------------------------------------------------------

1.  Download the necessary components from [Prometheus.io](https://prometheus.io) using `wget`:

    ```
    wget <prometheus_download_link>
    wget <blackbox_exporter_download_link>
    wget <alertmanager_download_link>
    ```

2.  Extract the downloaded `.tar` files:

    ```
    tar -xvf prometheus-*.tar.gz
    tar -xvf blackbox_exporter-*.tar.gz
    tar -xvf alertmanager-*.tar.gz
    ```

3.  Remove the `.tar` files to free up space:

    ```
    rm prometheus-*.tar.gz blackbox_exporter-*.tar.gz alertmanager-*.tar.gz
    ```

4.  Rename the extracted directories for easier reference:

    ```
    mv prometheus-* prometheus
    mv blackbox_exporter-* blackbox
    mv alertmanager-* alertmanager
    ```

2\. Install and Start Node Exporter on Website Machine
------------------------------------------------------

1.  Install and start your application on the website machine.

2.  Install Node Exporter to scrape metrics from the website:

    ```
    wget <node_exporter_download_link>
    tar -xvf node_exporter-*.tar.gz
    rm node_exporter-*.tar.gz
    mv node_exporter-* node_exporter
    ```

3.  Start Node Exporter:

    ```
    ./node_exporter &
    ```

    *(Runs Node Exporter in the background to monitor system metrics.)*

3\. Start Prometheus Service
----------------------------

1.  Start Prometheus as a background process:

    ```
    ./prometheus &
    ```

2.  Create `alert_rules.yml` inside the Prometheus directory and define alerting rules.

4\. Configure Prometheus for Alerting Rules
-------------------------------------------

1.  Edit `prometheus.yml` and add the `rule_files` section:

    ```
    rule_files:
      - "alert_rules.yml"
    ```

2.  Restart Prometheus to apply changes:

    ```
    pgrep prometheus   # Get the process ID of Prometheus
    kill <process_id>  # Kill the running Prometheus process
    ./prometheus &     # Restart Prometheus in the background
    ```

5\. Connect Blackbox Exporter, Node Exporter, and Alertmanager to Prometheus
----------------------------------------------------------------------------

1.  Edit `prometheus.yml` to include scrape configurations for:

    -   Alertmanager (Port: 9093)

    -   Blackbox Exporter (Port: 9115)

    -   Node Exporter (Port: 9100)

2.  Add the necessary job configurations. For example:

    ```
    scrape_configs:
      - job_name: 'alertmanager'
        static_configs:
          - targets: ['localhost:9093']

      - job_name: 'blackbox'
        metrics_path: /probe
        params:
          module: [http_2xx]
        static_configs:
          - targets:
            - http://example.com
        relabel_configs:
          - source_labels: [__address__]
            target_label: __param_target
          - source_labels: [__param_target]
            target_label: instance
          - target_label: __address__
            replacement: localhost:9115

      - job_name: 'node_exporter'
        static_configs:
          - targets: ['localhost:9100']
    ```

3.  Restart Prometheus after making changes:

    ```
    pgrep prometheus | xargs kill
    ./prometheus &
    ```

6\. Configure Email Notifications in Alertmanager
-------------------------------------------------

1.  Edit `alertmanager.yml` located inside the Alertmanager directory.

2.  Configure email settings:

    ```
    route:
      receiver: 'email'
    receivers:
      - name: 'email'
        email_configs:
          - to: 'your-email@example.com'
            from: 'alertmanager@example.com'
            smarthost: 'smtp.example.com:587'
            auth_username: 'your-email@example.com'
            auth_password: 'your-password'
    ```

3.  Restart Alertmanager to apply changes:

    ```
    ./alertmanager &
    ```

Ports Used in the Setup
-----------------------

-   **Prometheus:** `9090`

-   **Alertmanager:** `9093`

-   **Blackbox Exporter:** `9115`

-   **Node Exporter:** `9100`

Conclusion
----------

By following these steps, you have successfully set up Prometheus, Blackbox Exporter, Alertmanager, and Node Exporter for monitoring your application and website. The setup includes alerting rules and email notifications to keep track of system performance and availability.



![image](https://github.com/user-attachments/assets/d41e8eb7-f8fe-419a-8c7e-39d49f4f3e55)
![image](https://github.com/user-attachments/assets/82d65e3f-6741-4039-a640-7cc79c6c2a1d)
![image](https://github.com/user-attachments/assets/04605db5-0661-43e0-a2e5-926074fd9e36)
![image](https://github.com/user-attachments/assets/75d486a3-472e-4893-86e1-ed5dadbc1539)
![image](https://github.com/user-attachments/assets/309a93f4-1bd1-4f0d-b66c-fd4e987ea899)
![image](https://github.com/user-attachments/assets/bf3ea4f0-ea06-4e68-b006-2e1738055eac)
![image](https://github.com/user-attachments/assets/3cfb9043-2cca-463f-a505-8a3f26c2b35b)
![image](https://github.com/user-attachments/assets/df82af12-5911-42e4-aad0-fc17b8dca422)
![image](https://github.com/user-attachments/assets/755560bc-1d8a-4b1e-82c8-b54cf82a46cc)
