# rust-metrics
[![Linux Status](https://travis-ci.org/posix4e/rust-metrics.svg?branch=master)](https://travis-ci.org/posix4e/rust-metrics)
[![Kanban](https://img.shields.io/github/issues/posix4e/rust-metrics.svg?label=HuBoard)](https://huboard.com/posix4e/rust-metrics#/?repo=[%22huboard%22])

This is a work in progress. 
Metrics are things you can use to safely & directly store metrics with little overhead. Metrics
can be attached to a registry and that registry can be collected across a system. This registry
also provides reporting services. Current reporters include:

- [Prometheus](https://prometheus.io/) with included [prometheues reporter](prometheus_reporter)
- Graphite/Carbon/Whisper
- Console/Syslog/Journald (via stdout)

Contact us on #rust-metrics on Mozilla IRC.

```rust
...
        let m = StdMeter::new();
        m.mark(100);

        let c = StdCounter::new();
        c.inc();

        let g = StdGauge::new();
        g.set(2);

        let mut h = Histogram::configure()
            .max_value(100)
            .precision(1)
            .build()
            .unwrap();

        h.increment_by(1, 1).unwrap();

        let mut reporter = CarbonReporter::new("test", "localhost:0".to_string(), "asd.asdf");
        reporter.start(5);
        reporter.add("meter1", Metric::Meter(m.clone()));
        reporter.add("counter1", Metric::Counter(c.clone()));
        reporter.add("gauge1", Metric::Gauge(g.clone()));
        reporter.add("histogram", Metric::Histogram(h));
        loop {    c.inc()}
```


## Usage


Add this to your `Cargo.toml`:

```toml
"metrics" = "*"
```

And add this to your crate root:

```rust
extern crate metrics
```
## Provided scripts in bin/

* **build_docker** This builds the default docker image
* **generate_pb** Generates the prometheus protocol buffer code
* **run_docker** This will run the  docker container once it's been built (or download the last one i pushed)
* **start_docker** Use docker_compose  to launch prometheus, carbon/graphite and clients which send them data
* **webserver_with_prometheus** Starts a webserver which runs with a prometheus reporter
* **start_prometheus_example** Use docker-compose to start a prometheus server & hook it up to webserver_with_prometheus
* **webserver_with_carbon** Starts a webserver with a carbon reporter
* **start_carbon_example** Use docker-compose to start graphite/carbon/whisper and hook it up to webserver_with_carbon

Many of these will only run if prometheus support is enabled

## TBD
- [ ] C ffi
- [ ] js ffi
- [ ] Multi Reporter with thresholding
- [ ] Switch to https://github.com/pingcap/rust-prometheus
- [ ] Tested in Production at Scale on AWS (10k-20k) vms

## Local Development

```
    cargo build # to build the code
    cargo test # to run the tests
```

To use prometheus you will might need to read [these directions](prometheus_reporter/README.md)
```
    cargo build --features "prometheus" # To build code with prometheus support
```
## License

`rust-metrics` is primarily distributed under the terms of both the MIT license and the
Apache License (Version 2.0).

See [LICENSE-APACHE](LICENSE-APACHE), [LICENSE-MIT](LICENSE-MIT) for details.

Copyright (c) 2016 Alex Newman.
