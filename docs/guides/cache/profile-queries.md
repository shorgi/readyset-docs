# Profile Queries

After [connecting your app](../connect/existing-app.md) to ReadySet, profile your app performance and identify queries to cache in ReadySet. Generally, it's best to focus on frequent queries that are too slow and/or that are putting unwanted load on your upstream database.

If you already have performance monitoring in place, use that tooling to identify queries that can benefit from caching in ReadySet. Otherwise, you can use ReadySet's own metrics endpoint to profile queries.

## Enable ReadySet metrics

To enable ReadySet metrics, start ReadySet with the following options:

- [`--prometheus-metrics`](../../reference/cli/readyset.md#-prometheus-metrics)
- [`--metrics-address`](../../reference/cli/readyset.md#-metrics-address)

To include query-specific execution metrics, also pass:

- [`--query-log`](../../reference/cli/readyset.md#-query-log)
- [`--query-log-ad-hoc`](../../reference/cli/readyset.md#-query-log-ad-hoc)

## Access ReadySet metrics

You can access ReadySet metrics at `<metrics address>/metrics`, where the metrics address is defined by the [`--metrics-address`](../../reference/cli/readyset.md#-metrics-address) option (default: `0.0.0.0:6034/metrics`).

!!! tip

    When [running ReadySet locally](../intro/quickstart.md), you can usually access ReadySet metrics at `https://127.0.0.1:6034/metrics` in your browser.

## Examine per-query metrics

ReadySet metrics are formatted for easy integration with [Prometheus](https://prometheus.io/). However, the quickest way to examine per-query metrics is to use a simple metrics utility written in Python that queries the metrics endpoint and displays latencies for queries received by ReadySet.

1. Download the metrics utility and its `requirements.txt`:

   ```sh
   curl -O "https://raw.githubusercontent.com/readysettech/docs/main/docs/assets/metrics/metrics.py"
   curl -O "https://raw.githubusercontent.com/readysettech/docs/main/docs/assets/metrics/requirements.txt"
   ```

   1. Install dependencies for the utility:

   ```sh
   pip3 install -r requirements.txt
   ```

   1. Set the `HOST` environment variable to the IP address/hostname portion of [`--metrics-address`](../../reference/cli/readyset.md#-metrics-address):

   ```sh
   export HOST="<metrics-host>"
   ```

   1. Run the script with

   ```sh
   python3 metrics.py --host=${HOST}
   ```

   You can filter the output of this script to show only queries displayed in [`SHOW PROXIED QUERIES`](../cache/cache-queries.md#check-query-support) or [`SHOW CACHES`](../cache/cache-queries.md#view-cached-queries) by passing the `--filter-queries` flag and piping the output of those ReadySet commands into the script like so:

   ```sh
   PGPASSWORD=noria psql --host=127.0.0.1 --port=5433 --username=postgres --dbname=noria -c "SHOW CACHES" | python3 metrics.py --filter-queries
   ```
