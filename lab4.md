LAB 4

**MONITORING & PRODUCTION DEPLOYMENT**

_Observability, Alerting, and Production-Ready ML Systems_

| Course        | DDM501                          |
| ------------- | ------------------------------- |
| Weight        | 15%                             |
| Format        | Team Lab (3-4 members per team) |
| Prerequisites | Labs 1, 2, and 3 completed      |

# 1\. OVERVIEW

## 1.1. Introduction

The purpose of this lab is to implement comprehensive monitoring and observability for your movie rating prediction system. Production ML systems require constant monitoring to ensure they perform correctly, detect issues early, and maintain reliability.

You will set up Prometheus for metrics collection, Grafana for visualization, implement custom ML-specific metrics, create alerting rules, and deploy your application with production-ready monitoring infrastructure.

## 1.2. Scenario: Production Readiness

Your movie rating prediction system is now deployed in production. The operations team needs to:

- Monitor system health (CPU, memory, request rates)
- Track model performance (prediction latency, accuracy drift)
- Get alerted when things go wrong (high error rates, slow responses)
- Visualize trends and investigate issues

# 2\. BACKGROUND KNOWLEDGE

## 2.1. Observability Pillars

Observability consists of three main pillars:

| Pillar  | Description                                                   | Tools                            |
| ------- | ------------------------------------------------------------- | -------------------------------- |
| Metrics | Numeric measurements over time (counters, gauges, histograms) | Prometheus, StatsD, CloudWatch   |
| Logs    | Discrete events with context and details                      | ELK Stack, Loki, CloudWatch Logs |
| Traces  | Request flow across distributed services                      | Jaeger, Zipkin, OpenTelemetry    |

## 2.2. Prometheus Metrics Types

| Type      | Use Case                             | Example                         |
| --------- | ------------------------------------ | ------------------------------- |
| Counter   | Cumulative values that only increase | Total requests, errors          |
| Gauge     | Values that can go up or down        | Temperature, queue size         |
| Histogram | Distribution of values in buckets    | Request latency, response sizes |
| Summary   | Similar to histogram with quantiles  | Percentile latencies            |

## 2.3. ML-Specific Metrics

In addition to standard application metrics, ML systems need:

- Prediction Metrics: Distribution of predictions, prediction latency
- Data Quality Metrics: Feature distributions, missing values
- Model Health Metrics: Model version, last reload time
- Drift Metrics: Input drift, prediction drift, concept drift

# 3\. HANDS-ON GUIDE

## Task 1: Implement Application Metrics

1.1. Install Prometheus Client

_pip install prometheus-client==0.19.0_

1.2. Create Metrics Module

1.3. Add Metrics Middleware

1.4. Expose /metrics Endpoint

## Task 2: Track ML-Specific Metrics

2.1. Instrument Prediction Function

2.2. Track Error Metrics

## Task 3: Set Up Prometheus

3.1. Prometheus Configuration

3.2. Docker Compose with Prometheus

## Task 4: Create Grafana Dashboards

4.1. Add Grafana to Docker Compose

4.2. Grafana Data Source Provisioning

4.3. Dashboard Panels (Key Queries)

## Task 5: Configure Alerting

5.1. Prometheus Alert Rules

5.2. ML-Specific Alerts

## Task 6: Load Testing & Verification

6.1. Create Load Test Script

6.2. Verify Metrics in Grafana

After running the load test, verify in Grafana that:

- Request rate increases during the test
- Latency histograms show expected distribution
- Prediction values are distributed between 1-5
- No alerts are firing (if system is healthy)

# 4\. STARTER CODE TEMPLATE

Unzip starter code:

_unzip ddm501-lab4-starter.zip_

Files to complete:

| **File**                   | **TODO Items**                                        |
| -------------------------- | ----------------------------------------------------- |
| app/metrics.py             | Define Prometheus metrics (Counter, Histogram, Gauge) |
| app/middleware.py          | Implement metrics middleware                          |
| prometheus/prometheus.yml  | Configure scrape targets                              |
| prometheus/alerts/\*.yml   | Create alerting rules                                 |
| grafana/dashboards/\*.json | Create monitoring dashboards                          |
| docker-compose.yml         | Add Prometheus and Grafana services                   |
| scripts/load_test.py       | Implement load testing script                         |

# 5\. DELIVERABLES & GRADING

## 5.1. Deliverables

- Instrumented Application: API with Prometheus metrics exposed
- Prometheus Setup: Working Prometheus configuration with scraping
- Grafana Dashboards: At least 2 dashboards (System & ML metrics)
- Alert Rules: At least 5 meaningful alerting rules
- Load Test Results: Screenshots showing metrics during load test
- Documentation: README with setup instructions and dashboard guide

## 5.2. Grading Rubric

| **Criteria**        | **Weight** | **Description**                                                                     |
| ------------------- | ---------- | ----------------------------------------------------------------------------------- |
| Application Metrics | 25%        | Request metrics (8%)<br><br>ML-specific metrics (10%)<br><br>/metrics endpoint (7%) |
| Monitoring Stack    | 25%        | Prometheus configuration (10%)<br><br>Grafana setup (8%)<br><br>Docker Compose (7%) |
| Dashboards & Alerts | 30%        | System dashboard (10%)<br><br>ML metrics dashboard (10%)<br><br>Alert rules (10%)   |
| Documentation       | 20%        | Setup guide (8%)<br><br>Dashboard documentation (7%)<br><br>Load test report (5%)   |

## 5.3. Submission

Deadline: 1 week after the lab session

Format: GitHub repository link

Required: Screenshots of Grafana dashboards with data
