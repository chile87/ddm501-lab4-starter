# DDM501 Lab 4 - Rubric Checklist

Checklist này bám theo `lab4.md`: Monitoring & Production Deployment.

## Kết Quả Kiểm Tra Lần Này

- [x] `pytest -q`: 15 tests passed.
- [x] Python syntax check cho `app/*.py` và `scripts/load_test.py`.
- [x] JSON dashboard và YAML Prometheus/alerts hợp lệ.
- [x] 10/10 metrics đã được định nghĩa.
- [x] Có 8 alert rules, vượt mức tối thiểu 5.
- [x] `docker compose config` chạy thành công.
- [x] Load test thật đã chạy với model: 2,400 requests, 100% success, 80 req/s.
- [x] Load test xác nhận lần 2: 1,570 requests, 100% success, 78.5 req/s.
- [x] Screenshot System Dashboard: `screenshots/grafana-system-dashboard.png`.
- [x] Screenshot ML Dashboard: `screenshots/grafana-ml-dashboard.png`.
- [x] GitHub repository link do người dùng đã upload.

## Tổng Quan Điểm

| Nhóm | Điểm |
|---|---:|
| Application Metrics | 25% |
| Monitoring Stack | 25% |
| Dashboards & Alerts | 30% |
| Documentation | 20% |
| **Tổng** | **100%** |

## 1. Application Metrics (25%)

### 1A. Request Metrics (8%)

- [ ] `REQUEST_COUNT` là Counter với labels `method`, `endpoint`, `status`.
- [ ] `REQUEST_LATENCY` là Histogram với buckets latency HTTP.
- [ ] `MetricsMiddleware` đo thời gian, status và ghi cả request count/latency.
- [ ] Middleware được đăng ký trong `app/main.py`.

Kiểm tra:

```bash
python -c "from app.metrics import REQUEST_COUNT, REQUEST_LATENCY; assert REQUEST_COUNT and REQUEST_LATENCY; print('request metrics OK')"
pytest -q tests/test_metrics.py
```

### 1B. ML-Specific Metrics (10%)

- [ ] `ml_predictions_total` và `ml_prediction_duration_seconds` được ghi khi predict.
- [ ] `ml_prediction_value` theo dõi phân phối rating.
- [ ] `ml_prediction_errors_total` ghi lỗi theo `error_type` và `model_version`.
- [ ] `ml_model_loaded`, `ml_model_last_reload_timestamp`, `ml_model_info` phản ánh model health.
- [ ] Batch prediction ghi `ml_batch_prediction_size`.

Kiểm tra:

```bash
python -c "from app.metrics import count_implemented_metrics; print(count_implemented_metrics()); assert count_implemented_metrics()[0] >= 9"
```

### 1C. `/metrics` Endpoint (7%)

- [ ] `GET /metrics` trả HTTP 200.
- [ ] Response dùng Prometheus text format.
- [ ] Response có `http_requests_total` và các metric ML.

Kiểm tra:

```bash
python -c "from fastapi.testclient import TestClient; from app.main import app; r=TestClient(app).get('/metrics'); assert r.status_code == 200; assert 'http_requests_total' in r.text; print('/metrics OK')"
```

## 2. Monitoring Stack (25%)

### 2A. Prometheus Configuration (10%)

- [ ] Scrape job `movie-rating-api` target `api:8000`.
- [ ] Metrics path là `/metrics` và interval là `10s`.
- [ ] Rule files gồm `alerts/*.yml`.

Kiểm tra:

```bash
python -c "import yaml; c=yaml.safe_load(open('prometheus/prometheus.yml')); jobs={j['job_name']:j for j in c['scrape_configs']}; assert jobs['movie-rating-api']['static_configs'][0]['targets']==['api:8000']; print('Prometheus config OK')"
```

### 2B. Grafana Setup (8%)

- [ ] Prometheus datasource được provision tự động.
- [ ] Có dashboard provider trỏ tới `/var/lib/grafana/dashboards`.
- [ ] Có `system_dashboard.json` và `ml_dashboard.json` hợp lệ.

### 2C. Docker Compose (7%)

- [ ] Có services `api`, `prometheus`, `grafana`.
- [ ] Prometheus expose port `9090`, Grafana expose port `3000`.
- [ ] Có named volumes và monitoring network.
- [ ] Grafana phụ thuộc Prometheus; Prometheus đợi API healthy.

Kiểm tra:

```bash
docker compose config
```

## 3. Dashboards & Alerts (30%)

### 3A. System Dashboard (10%)

- [ ] Có panel request rate.
- [ ] Có panel P95 request latency.
- [ ] Có panel error rate.
- [ ] Có panel status code distribution.
- [ ] Có panel requests by endpoint.

### 3B. ML Metrics Dashboard (10%)

- [ ] Có panel model status.
- [ ] Có panel prediction rate và P95 prediction latency.
- [ ] Có panel prediction value distribution và average prediction.
- [ ] Có panel prediction errors và model info.

Kiểm tra JSON:

```bash
python -c "import json; [json.load(open(p)) for p in ['grafana/dashboards/system_dashboard.json','grafana/dashboards/ml_dashboard.json']]; print('dashboard JSON OK')"
```

### 3C. Alert Rules (10%)

- [ ] Có ít nhất 5 alert rules meaningful.
- [ ] Bao gồm HighErrorRate, HighLatency, ServiceDown.
- [ ] Bao gồm ModelNotLoaded, PredictionLatencyHigh, LowPredictionVolume hoặc tương đương.
- [ ] Alert có `for`, `severity`, `summary`, `description`.

Kiểm tra:

```bash
python -c "import yaml, glob; rules=sum(len(g.get('rules',[])) for p in glob.glob('prometheus/alerts/*.yml') for g in yaml.safe_load(open(p))['groups']); print('alert rules:', rules); assert rules >= 5"
```

## 4. Documentation (20%)

### 4A. Setup Guide (8%)

- [ ] README hướng dẫn cài dependencies và chạy API.
- [ ] README hướng dẫn `docker compose up -d`.
- [ ] README ghi URL API, Prometheus, Grafana và credentials.

### 4B. Dashboard Documentation (7%)

- [ ] README mô tả system dashboard.
- [ ] README mô tả ML dashboard.
- [ ] README có các PromQL query chính.

### 4C. Load Test Report (5%)

- [ ] `scripts/load_test.py` chạy được với `--duration`, `--workers`, `--batch`.
- [ ] Có thống kê total, success rate, requests/second và latency percentiles.
- [ ] Có screenshot Grafana trong submission sau khi chạy load test.
- [ ] Có GitHub repository link khi nộp bài.

Kiểm tra syntax:

```bash
python -m py_compile app/*.py scripts/load_test.py
```

## 5. Submission Evidence

- [x] Screenshot Grafana System Dashboard có dữ liệu.
- [x] Screenshot Grafana ML Metrics Dashboard có dữ liệu.
- [ ] Screenshot Prometheus targets/rules nếu được yêu cầu.
- [x] Link GitHub repository.

## 6. Quick Verification

```bash
pytest -q
python -c "import json, glob, yaml; [json.load(open(p)) for p in glob.glob('grafana/dashboards/*.json')]; [yaml.safe_load(open(p)) for p in ['prometheus/prometheus.yml', *glob.glob('prometheus/alerts/*.yml')]]; print('all configs OK')"
docker compose config
```
