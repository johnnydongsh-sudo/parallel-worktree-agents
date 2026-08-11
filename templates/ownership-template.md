# 文件所有权表（写入 Phase 0 契约）

规则：一个共享文件在同一批并行任务中只能有一个 Worker 拥有写权限。按**文件**登记，不按目录。

## 本批任务

| 文件 | 唯一 Owner | 备注 |
|---|---|---|
| pubspec.yaml | Coordinator | 本批禁止修改 |
| pubspec.lock | Coordinator | 本批禁止修改 |
| lib/services/storage_service.dart | Coordinator | 本批禁止修改 |
| <file A> | Worker <name> | |
| <file B> | Worker <name> | |

## 典型共享文件默认 Owner

| 文件类型 | 默认负责人 |
|---|---|
| 共享业务代码 | Shared Worker |
| Podfile.lock | iOS Worker |
| Gradle 配置 | Android Worker |
| 公共 API schema | Contract Worker 或 Coordinator |
| 根目录 lockfile | Shared Worker |
| 最终版本号 | Coordinator |

其他 Worker 发现必须修改共享文件 → BLOCKED 申报，不得直接修改。
