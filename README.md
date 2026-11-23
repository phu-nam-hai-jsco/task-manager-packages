# task-manager-packages

Public **host** của Docker images cho **Quản Lý Công Việc** (GHCR). Workflow publish chạy
tại repo này nên package tự động link về đây và **public** (pull không cần PAT).

| Image (ghcr.io/phu-nam-hai-jsco/...) | Lane main | Lane dev | Mô tả |
| ------------------------------------ | --------- | -------- | ----- |
| `task-manager-server` (+ `-dev`)     | `latest`, `X.Y.Z`, `sha-*` | `latest`, `X.Y.Z` | ASP.NET Core 11 API |
| `task-manager-web` (+ `-dev`)        | `latest`, `X.Y.Z`, `sha-*` | `latest`, `X.Y.Z` | Next.js static export + reverse proxy |
| `task-manager-migrator` (+ `-dev`)   | `latest`, `X.Y.Z`          | `latest`, `X.Y.Z` | DB migrator (Prisma)  |

Cơ chế: `task-manager` (private) gửi `repository_dispatch` → workflow publish tại đây gọi
reusable build workflows từ [`task-manager-action`](https://github.com/phu-nam-hai-jsco/task-manager-action)
→ build từ source của `task-manager` (checkout bằng secret `SUBMODULE_TOKEN`) → push GHCR.

Secret cần thiết: `SUBMODULE_TOKEN` (PAT scope `repo`).

## 🧹 Dọn lịch sử Actions

`.github/workflows/cleanup-runs.yml` dùng [`igorjs/gh-actions-clean-workflow`](https://github.com/igorjs/gh-actions-clean-workflow) (ghim SHA),
chạy **hằng ngày 02:17 UTC** hoặc chạy tay (mặc định `dry_run = true` để xem trước).
Chỉ xoá run **đã hoàn thành** cũ hơn `runs_older_than` ngày (mặc định 7); trong số đó giữ lại `runs_to_keep` run mới nhất
của mỗi workflow (mặc định 3). `workflow_names` (tuỳ chọn) giới hạn theo tên workflow.
Mỗi lần chạy xoá tối đa ~1000 run (giới hạn API GitHub); lịch hằng ngày dọn dần phần tồn đọng.
