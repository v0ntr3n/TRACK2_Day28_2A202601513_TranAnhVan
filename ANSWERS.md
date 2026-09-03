# Báo Cáo Kỹ Thuật & Hồ Sơ Nộp Bài — Day 28 Track 2
# Platform Integration & Production Readiness

* **Học viên:** Trần Anh Văn
* **Mã học viên:** `2A202601513`
* **Vai trò:** Platform Engineer / AI Infrastructure Engineer (Thực hiện toàn diện lộ trình cá nhân — đi qua đủ 5 vai trò)
* **Khóa học:** VinUni AI20k · AICB Phase 2 · Track 2 (AI Infrastructure & Platform Engineering) · Day 28
* **URL Kho lưu trữ & Nhánh nộp bài:** [https://github.com/v0ntr3n/TRACK2_Day28_2A202601513_TranAnhVan](https://github.com/v0ntr3n/TRACK2_Day28_2A202601513_TranAnhVan) (nhánh `main`)

---

## 1. Thông Tin Kho Mã Nguồn & Nhánh Nộp Bài (Repository & Branch)

* **Repository Remote:** `origin`
* **Private Repository URL:** `https://github.com/v0ntr3n/TRACK2_Day28_2A202601513_TranAnhVan`
* **Nhánh nộp bài (Target Branch):** `main`
* **Upstream Scaffold:** `https://github.com/VinUni-AI20k/Day28-Modern-Platform-Lab-Student.git`
* **Cam kết an toàn:** Không commit tệp `.env`, API keys, database binaries, checkpoints, hay thư mục tạm `.lab28/`.

---

## 2. Danh Mục Gói Bằng Chứng Tích Hợp (Evidence Bundle)

Toàn bộ gói bằng chứng gồm **12 tệp** được xuất đầy đủ trong thư mục `evidence/` theo chuẩn machine-readable của `contracts/integration-matrix.yaml`:

| STT | Tệp bằng chứng | Điểm kết nối (IP) | Mô tả nội dung | Trạng thái |
|:---:|:---|:---:|:---|:---:|
| 1 | `evidence/integration-report.json` | Tổng hợp | Báo cáo đánh giá tổng thể trạng thái sẵn sàng 10 điểm kết nối từ hàm `readiness.integration_report()`. | **Verified** |
| 2 | `evidence/ip01-kafka-consume.json` | IP01 | Bản tin `data.raw` chứa `idempotency-key` và W3C `traceparent` (bytes) do `event_headers()` sinh ra. | **Verified** |
| 3 | `evidence/ip02-airflow-run.json` | IP02 | Nhật ký thực thi Airflow 3 DAG run, trạng thái các task và sự kiện phát asset `lab28://delta/feedback`. | **Verified** |
| 4 | `evidence/ip03-delta-history.json` | IP03 | Transaction log Delta Lake, phiên bản bảng `feedback`/`documents`, chứng minh MERGE time travel. | **Verified** |
| 5 | `evidence/ip04-feast-online.json` | IP04 | Bản ghi đặc trưng thực thể `asker_id` từ feature service `asker_serving_v1` do `feast_online_request()` gọi. | **Verified** |
| 6 | `evidence/ip05-qdrant-search.json` | IP05 | Kết quả tìm kiếm lai (hybrid search) với UUID tất định từ `doc_id` qua hàm `stable_point_id()`. | **Verified** |
| 7 | `evidence/ip06-mlflow-release.json` | IP06 | Phiên bản mô hình gắn alias `champion`, signature, prompt template, và git commit provenance. | **Verified** |
| 8 | `evidence/ip07-vllm-identity.json` | IP07 | Định danh vLLM build 0.28, mô hình Qwen, cấu hình FP8 KV-cache, chunked prefill và chuỗi metric `vllm:`. | **Verified** |
| 9 | `evidence/ip08-gateway.json` | IP08 | Bản ghi HTTP Envoy Gateway: định tuyến thành công (200 OK), bảo toàn `x-request-id`, và phản hồi 429 Rate Limit. | **Verified** |
| 10 | `evidence/ip09-prometheus-targets.json` | IP09 | Cấu hình scrape targets cho toàn bộ microservices và 3 quy tắc cảnh báo SLO (Latency, Lag, Degraded). | **Verified** |
| 11 | `evidence/ip10-trace.json` | IP10 | Dấu vết phân tán OpenTelemetry với 8 spans xuyên suốt từ Gateway $\to$ API $\to$ Kafka $\to$ Spark $\to$ Serving. | **Verified** |
| 12 | `evidence/test-suite-output.txt` | Toàn diện | Raw log chạy thực tế toàn bộ các test suites và static matrix validations (100% PASS). | **Verified** |

---

## 3. Kết Quả Kiểm Thử Phần Mã & Ma Trận Tích Hợp (Test Results & Matrix Validation)

Tất cả các bài kiểm tra chất lượng và hợp đồng ma trận đều đạt **100% PASS (Exit code 0)**:

```text
================================================================================
DAY 28 TEST SUITE & VALIDATION LOG — SUBMISSION EVIDENCE
Timestamp: 2026-09-03T23:30:50Z
Student: Tran Anh Van (ID: 2A202601513)
================================================================================
CMD: uv run --extra dev pytest starter-tests tests -q
EXIT CODE: 0
STDOUT:
........................................................................ [ 82%]
...............                                                          [100%]
87 passed in 3.42s
------------------------------------------------------------
CMD: uv run python scripts/verify_matrix.py
EXIT CODE: 0
STDOUT:
OK    245 checks passed: contracts\integration-matrix.yaml matches the repository
------------------------------------------------------------
CMD: uv run python scripts/check_portability.py
EXIT CODE: 0
STDOUT:
OK    supported workflow is host-path and shell independent
------------------------------------------------------------
CMD: uv run python scripts/validate_manifests.py
EXIT CODE: 0
STDOUT:
Kubernetes and GitOps manifest contracts passed
------------------------------------------------------------
CMD: uv run ruff check .
EXIT CODE: 0
STDOUT:
All checks passed!
================================================================================
```

* **Starter Tests:** 4/4 passed (100%) — Hoàn thiện cả 4 hàm boundary sinh viên phụ trách.
* **Unit Test Suite:** 83/83 passed (100%) — Giải quyết triệt để 14 test cases từng fail do stubs ban đầu.
* **Static Matrix Checks:** 245/245 checks passed — Khớp hoàn hảo giữa tài liệu slide, YAML contracts và mã nguồn.
* **Linter & Portability:** Không có lỗi định dạng hay phụ thuộc hệ điều hành.

---

## 4. Chứng Minh Luồng Đúng, Tính Replay-Safe, Metrics & Traces

### 4.1. Sơ Đồ Kiến Trúc Hệ Thống (Architecture Flow)

Tham chiếu sơ đồ trực quan tại [`docs/images/lab28-architecture-overview.png`](docs/images/lab28-architecture-overview.png):

```mermaid
flowchart TD
    Client["Client / Upstream Ingress"] -->|HTTP / x-request-id| Gateway["Envoy Gateway (IP08)"]

    subgraph L1["L1 Compute & Serving"]
        Gateway -->|Route /api/v1/chat| API["FastAPI Serving Pod"]
        API -->|OpenAI Protocol (IP07)| vLLM["vLLM Engine (Qwen3.5-2B / FP8)"]
    end

    subgraph L2["L2 Data Platform"]
        API -->|Publish IngestionEvent (IP01)| Kafka["Apache Kafka (data.raw)"]
        Kafka -->|W3C Traceparent Header (IP02)| Airflow["Apache Airflow 3"]
        Airflow -->|Spark Connect MERGE (IP03)| Delta["Delta Lake Tables"]
        Delta -->|Deduplicated Points (IP05)| Qdrant["Qdrant Vector DB"]
        Delta -->|Offline Snapshot (IP04)| Feast["Feast Feature Store"]
    end

    subgraph L3["L3 ML Platform"]
        MLflow["MLflow Model Registry (IP06)"] -->|Champion Model Alias| API
        Feast -->|Online Feature Lookup (IP04)| API
        Qdrant -->|Grounding Context (IP05)| API
    end

    subgraph L4["L4 Observability & Ops"]
        API & Gateway & Kafka & Airflow & Feast & Qdrant & vLLM -.->|OTLP Spans (IP10)| OTEL["OTEL Collector / Jaeger"]
        API & Gateway & Feast & vLLM -.->|Metrics Scrape (IP09)| Prometheus["Prometheus & Grafana"]
    end
```

### 4.2. Chứng Minh Tính An Toàn Khi Replay Dữ Liệu (Replay-Safe & Idempotency Proof)

* **Vấn đề cốt lõi:** Khi Kafka consumer crash hoặc gặp sự cố mạng, một batch có thể bị gửi lại nhiều lần (at-least-once delivery). Nếu ghi thẳng vào Delta Lake, lệnh `MERGE` sẽ fail ngay lập tức do trùng lặp bản ghi nguồn cùng key, hoặc tạo ra các bản ghi trùng lặp (duplicate rows).
* **Giải pháp trong hàm `dedupe_latest()`:**
  1. Gom nhóm toàn bộ bản tin theo `idempotency_key`.
  2. Với mỗi khóa, so sánh tuple `(occurred_at, event_id)`. Bản tin có thời điểm phát sinh muộn nhất (hoặc `event_id` lớn nhất khi trùng timestamp) được giữ lại, loại bỏ toàn bộ bản tin cũ.
  3. Kết quả đầu ra được sắp xếp tăng dần theo `idempotency_key` nhằm đảm bảo **tính tất định 100% (deterministic output)** bất kể thứ tự các partition của Kafka gửi đến.
* **Chứng thực kiểm thử:** Bộ test [`tests/test_delta_merge_idempotency.py`](tests/test_delta_merge_idempotency.py) kiểm thử trường hợp batch được nhân 3 lần (`batch * 3`), kết quả ghi vào Delta Lake chỉ giữ đúng duy nhất số dòng tương ứng với các khóa đơn lẻ, số dòng mục tiêu cập nhật chính xác (`numTargetRowsUpdated: 1, numTargetRowsInserted: 0`).

### 4.3. Giám Sát Tín Hiệu Vàng (Golden Signals & Prometheus Alerts)

Hệ thống cung cấp đầy đủ 4 tín hiệu vàng SRE:
1. **Latency:** `lab28_request_seconds_bucket`, `lab28_llm_seconds_bucket`, `lab28_feature_lookup_seconds`.
2. **Traffic:** `lab28_requests_total`, `envoy_http_downstream_rq_total`.
3. **Errors:** `lab28_requests_total{status=~"5.."}` và `lab28_consumer_lag`.
4. **Saturation:** `vllm:gpu_cache_usage_factor`, `lab28_readiness_score`.

### 4.4. Tính Liên Tục Của Dấu Vết Phân Tán (Distributed Trace Continuity)

Mỗi yêu cầu từ người dùng được gắn mã W3C `traceparent` (ví dụ: `00-4bf92f3577b34da6a3ce929d0e0e4736-00f067aa0ba902b7-01`):
* `trace_id` (32 ký tự hexa) được giữ **bất biến** qua mọi ranh giới phân tán: Gateway $\to$ FastAPI $\to$ Kafka byte-header $\to$ Airflow DAG $\to$ Spark Delta MERGE $\to$ Vector Search $\to$ vLLM $\to$ Client Response.
* Nhờ đó, kỹ sư vận hành có thể tra cứu duy nhất 1 Trace ID trên Jaeger / LangSmith để thấy toàn bộ cây span 8 tầng.

---

## 5. Ghi Chú Xử Lý Sự Cố Thực Nghiệm (Incident & RCA Note)

### Sự Cố 1: Cơn Bão Replay Bản Tin Kafka (Data Plane Replay Storm)
* **Kịch bản mô phỏng:** Giả lập consumer bị crash đột ngột trong khi đang commit offset vào Kafka, khiến Kafka gửi lại (re-deliver) một batch 50 bản tin feedback cũ kèm theo 2 bản tin cập nhật rating mới.
* **Dấu hiệu quan sát:**
  * Metric `lab28_consumer_lag` đột ngột tăng vọt.
  * Log pipeline cảnh báo nhận được các `idempotency_key` đã từng tồn tại trong Delta table.
* **Nguyên nhân gốc rễ (Root Cause):** Mạng phân tán không thể đảm bảo Exactly-Once delivery ở tầng transport; broker phải retransmit để tránh mất mát dữ liệu.
* **Khôi phục & Phòng vệ:**
  * Bộ lọc `dedupe_latest()` nhận diện các key trùng, lấy bản tin mới nhất theo timestamp và loại bỏ 48 bản tin dư thừa.
  * Lệnh Delta MERGE thực thi an toàn với 2 bản ghi cập nhật và 0 bản ghi lỗi, bảo toàn tính toàn vẹn và không mất mát dữ liệu (**Zero Data Loss**).

### Sự Cố 2: Thành Phần Tùy Chọn Mất Kết Nối (Degraded Serving Mode)
* **Kịch bản mô phỏng:** Giả lập Feast Feature Store container bị treo hoặc mất kết nối mạng (`ConnectError: [WinError 10061]`).
* **Dấu hiệu quan sát:**
  * Endpoint `/ready` chuyển từ trạng thái `"ready"` sang `"degraded"`.
  * Metric `lab28_component_ready{component="feature-store"}` chuyển về giá trị `0`.
* **Nguyên nhân gốc rễ (Root Cause):** Service Feast bị timeout hoặc quá tải tài nguyên.
* **Khôi phục & Phòng vệ:**
  * Do `readiness_status()` đánh giá Feast là thành phần tùy chọn (`mandatory=False`), hệ thống **không ngắt kết nối** của pod khỏi Envoy Gateway (không trả về 503).
  * API phục vụ kích hoạt cơ chế Cold-Start Fallback (dùng giá trị mặc định `avg_rating: 5.0, feedback_count: 0`) để tiếp tục trả lời câu hỏi của người dùng bình thường.
  * Khởi động lại Feast container, metric tự phục hồi về `1` và endpoint `/ready` trở lại trạng thái `"ready"` mà không gây gián đoạn dịch vụ người dùng.

---

## 6. Đánh Đổi Kỹ Thuật & Chiêm Nghiệm (Trade-offs & Technical Reflection)

### 6.1. Điều Khó Nhất Trong Quá Trình Thực Hiện
* **Bài toán:** Đảm bảo tính tất định tuyệt đối cho Delta Lake MERGE khi nhận dữ liệu từ Kafka.
* **Phân tích:** Kafka lưu trữ dữ liệu trên nhiều partition, thứ tự bản tin nhận được tại consumer phụ thuộc vào độ trễ mạng và thời điểm đọc của từng worker thread. Nếu chỉ lấy bản tin xuất hiện sau cùng trong danh sách nhận được, kết quả sẽ bị sai lệch ngẫu nhiên.
* **Giải pháp:** Thiết kế thuật toán tie-breaking dựa trên tuple giá trị nghiệp vụ `(occurred_at, event_id)` và bắt buộc sort mảng kết quả theo `idempotency_key` trước khi trả về cho Spark Connect.

### 6.2. Các Đánh Đổi Thiết Kế Đã Chọn (Architectural Trade-offs)
1. **Deduplication Tầng Ứng Dụng vs. Phụ Thuộc Hoàn Toàn Vào Delta Lake Engine:**
   * *Đã chọn:* Lọc trùng lặp bằng Python thuần trước khi chuyển payload qua gRPC cho Spark Connect.
   * *Đánh đổi:* Chấp nhận tốn thêm một lượng nhỏ CPU/RAM tại pod Ingestion để đổi lại sự an toàn: loại bỏ triệt để các ngoại lệ va chạm khóa tại JVM của Spark, giúp hệ thống dễ viết test và kiểm chứng nhanh chóng (fast tests runnable in seconds without JVM).
2. **Cơ Chế Sẵn Sàng Phân Cấp (Graceful Degradation) vs. Fail-Fast Tuyệt Đối:**
   * *Đã chọn:* Cho phép hệ thống hoạt động ở chế độ `degraded` khi thiếu Feast hoặc MLflow.
   * *Đánh đổi:* Câu trả lời có thể kém cá nhân hóa hơn đôi chút trong giai đoạn sự cố, nhưng bảo vệ được tỷ lệ sẵn sàng dịch vụ (Service Availability SLO) đạt mức tối đa.
3. **Local Rate-Limiting Tại Envoy Gateway vs. Rate-Limiting Tại Ứng Dụng FastAPI:**
   * *Đã chọn:* Cấu hình giới hạn tần suất trực tiếp tại Envoy Edge.
   * *Đánh đổi:* Giảm tính linh hoạt trong việc cấu hình hạn mức động theo từng người dùng, nhưng bảo vệ hoàn toàn GIL của Python và tài nguyên tính toán của mô hình LLM khỏi các cuộc tấn công từ chối dịch vụ (DoS).

### 6.3. Những Điểm Sẽ Cải Tiến Khi Lên Môi Trường Production Thực Tế
1. **vLLM Autoscaling với KEDA:** Triển khai cơ chế mở rộng pod vLLM tự động dựa trên độ dài hàng đợi request (Queue Depth) và tỷ lệ đầy bộ nhớ KV-Cache (`vllm:gpu_cache_usage_factor`).
2. **Tail-based Sampling cho OpenTelemetry:** Cấu hình OTEL Collector để chỉ lưu 100% các trace có lỗi ($\ge 500$) hoặc độ trễ cao ($P99$), còn lại chỉ lấy mẫu $1\%$ nhằm tối ưu chi phí lưu trữ trên Cloud.
3. **Feast Real-time Streaming Ingestion:** Thay thế việc snapshot định kỳ bằng luồng streaming trực tiếp từ Kafka qua Apache Flink vào Redis Cluster để đặc trưng người dùng đạt độ tươi dưới 1 giây.

---

## 7. Bảng Phân Chia & Đảm Nhiệm Vai Trò Cá Nhân (Role Coverage Matrix)

Học viên **Trần Anh Văn (`2A202601513`)** thực hiện lộ trình cá nhân và đã đi qua đầy đủ **5 vai trò chuyên môn** theo quy định tại `docs/team-role-cards.md`:

| Vai trò đảm nhiệm | Điểm kết nối phụ trách | Nhiệm vụ kỹ thuật đã hoàn thành | Minh chứng cụ thể |
|:---|:---:|:---|:---|
| **1. Ingestion & Orchestration Engineer** | IP01, IP02 | Triển khai mã hóa byte header Kafka `event_headers()`, bảo toàn `idempotency-key` và W3C `traceparent`. Kết nối asset event Airflow. | `test_event_headers_preserve_trace_and_idempotency`, `evidence/ip01-kafka-consume.json` |
| **2. Data & ML Platform Engineer** | IP03, IP04, IP06 | Triển khai thuật toán chống trùng `dedupe_latest()` cho Delta MERGE; xây dựng request đọc đặc trưng Feast `feast_online_request()`; quản lý alias `champion` trên MLflow. | `test_delta_source_is_replay_safe_and_newest_wins`, `test_feast_request_matches_the_registry`, `evidence/ip03-delta-history.json` |
| **3. Serving & Retrieval Engineer** | IP05, IP07 | Chuẩn hóa UUID tất định cho Qdrant qua `stable_point_id()`; kết nối mô hình suy luận vLLM tốc độ cao (FP8 KV-cache, chunked prefill). | `test_the_document_is_retrievable_from_the_vector_store`, `evidence/ip05-qdrant-search.json`, `evidence/ip07-vllm-identity.json` |
| **4. Platform & Reliability / Observability Engineer** | IP08, IP09, IP10 | Hiện thực hàm `readiness_status()` với cơ chế phân tầng (Ready / Degraded / Not Ready); kiểm chứng rate-limiting Envoy Gateway; xác thực 245 kiểm tra ma trận tĩnh và manifest GitOps. | `test_readiness_distinguishes_failure_severity`, `scripts/verify_matrix.py`, `evidence/ip08-gateway.json`, `evidence/ip10-trace.json` |
| **5. Presenter & Incident Commander** | Toàn hệ thống | Xây dựng tài liệu báo cáo kỹ thuật `ANSWERS.md`, phân tích chuyên sâu các đánh đổi kiến trúc, soạn thảo hồ sơ sự cố RCA và giải pháp khắc phục không mất dữ liệu. | `ANSWERS.md`, `evidence/test-suite-output.txt`, `evidence/integration-report.json` |
