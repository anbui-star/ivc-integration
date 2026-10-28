# Tài liệu tích hợp D365FO ⇄ DMS: tiến độ

Mục đích: giúp developer phía ERP (D365FO) nắm cách DMS (Salesforce) tích hợp. Toàn bộ nằm trong **một file HTML** kiểu trang giấy A4: `Tich_hop_D365FO_DMS.html`. Các phần mới sẽ được thêm vào file này.

Ký hiệu: ✅ đã viết xong, chờ duyệt · 🔄 đang viết · ⬜ chưa viết

## Danh sách các phần

| | Phần | File | Ghi chú |
|---|---|---|---|
| ✅ | **Phần 1. Đồng bộ Master Data và Schedule Sync** (đã viết lại 2026-10-23) | trong `Tich_hop_D365FO_DMS.html` | Có thêm: tóm tắt, bản đồ thành phần (tên, label, đường dẫn source, nơi xem trên Setup), luồng 10 bước, bảng label ↔ API name của mọi field cấu hình, ví dụ một chu kỳ |
| ✅ | **Phần 2. Webhook Inbound (ERP → DMS)** | trong `Tich_hop_D365FO_DMS.html` | Endpoint `ApiD365FOHook`, request / response, 18 function, request mẫu thật từ Partial, thứ tự phụ thuộc |
| ✅ | **Phần 3. Webhook Outbound (DMS → ERP)** | trong `Tich_hop_D365FO_DMS.html` | 9 function đang chạy + 2 function đang tắt: endpoint, điều kiện kích hoạt, payload (kèm mẫu thật từ Partial), response ERP phải trả, vòng gửi đi / gửi về |
| ✅ | 3a. Nhóm Sales Order / Delivery Order | | `CreateSO`, `CreateDOPart`, `PostPackingSlip`, `ERP_SOCancelled` |
| ✅ | 3b. Nhóm PO / Shipment / Transfer Order | | `createPurchaseOrder`, `ERP_ShipmentCompleted`, `ERP_RegistrationPO_Loc`, `CreateTOVehicle`, `CreateTOPart` |
| ✅ | 3c. Nhóm đang tắt | | `4_SF_InventoryTransaction_Completed`, `5_SF_BinningSlip_Completed` |
| ✅ | **Phần 4. Bảng tra field mapping Master Data** | trong `Tich_hop_D365FO_DMS.html` | 29 bảng ERP: ý nghĩa bên ERP, object DMS, selectFields đang cấu hình, mapping field, dữ liệu đổ tiếp, ghi chú liên bảng (Product2, Vehicle__c, kho), bảng lookup resolver |
| ⬜ | **Phần 5. Vận hành và xử lý sự cố** | sẽ thêm vào `Tich_hop_D365FO_DMS.html` | Tra hook, retry, cảnh báo email, lệch giữa các org (Partial / QA / PROD) |
| ✅ | **Phụ lục A. Bản đồ mã nguồn của các Phần 2, 3, 4** | trong `Tich_hop_D365FO_DMS.html` | function_name → class xử lý, function Outbound → class gửi, nơi tạo hook và endpoint |
| ⬜ | **Phụ lục B. JSON mẫu và checklist thêm API mới** | | Gom từ các file `.json` / `.jsonc` trong `docs/Quy trình/` |

## Việc còn mở ở các phần đã viết

- 🔎 Phần 1: xác nhận với DMS admin cách ERP / job làm mới lịch hằng đêm trên QA và PROD (Partial chưa có job làm mới).
- 🔎 Phần 2: xác nhận cách cấp token Salesforce cho ERP (Connected App nào, user tích hợp nào); xác nhận `SO_Return` có an toàn khi gửi lại không; bổ sung request mẫu cho `1_IVCPurchTable_Confirm`, `3_InventTransfer_Shipped`, `9_PostInvoice` (loại không phải DropShip), `11_ScheduleOnhand`, `Sync_ResultTJ` khi có dữ liệu thật.
- 🔎 Phần 3: `CreateTOPart` và `ERP_SOCancelled` chưa có hook nào trên Partial nên chưa có request mẫu thật; nên đối chiếu `Receipt_PO_Local` (Phần 2) với payload `ERP_RegistrationPO_Loc`: payload chỉ gửi Id dòng PO và Id Shipment, không gửi Id `Shipment_Item__c` mà `Receipt_PO_Local` cần.
- 🔎 Phần 4: ý nghĩa bên ERP và mục "đổ tiếp" lấy từ `D365FO_DMS_Business_Mapping.md` (bản cũ, chưa cập nhật xử lý mới); cần rà lại nếu có thay đổi. `CustTransOpen` được bổ sung từ code. `selectFields` lấy từ Partial, QA / PROD có thể khác.
- 🔗 Link tới org Partial: mục 2.3 của Phần 1 (liên kết nhanh tới Setup, object, job) và link trực tiếp từng class / trigger trong các bảng. Link class dùng Id của Partial, đổi sau mỗi lần Partial refresh; `Sched_D365ScheduleRefresher` chưa có trên Partial.
