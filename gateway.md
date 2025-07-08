# API 网关需求梳理与Kong迁移方案

## 一、需求梳理

1. **按计算资源量限流**
   - 不同RPC方法分配不同权重（如eth_blockNumber=1，eth_getLogs=75等）
   - 按用户/Token/IP统计总消耗，超限拒绝请求
   - 支持自定义限流逻辑

2. **IP/域名白名单、黑名单**
   - 支持灵活配置允许/拒绝的IP、CIDR段、域名

3. **TLS证书管理**
   - 支持Let’s Encrypt自动申请与续期
   - 支持多域名、多证书

4. **多后端服务支持**
   - Node.js服务、Polkadot节点等多种后端
   - 支持负载均衡、健康检查

5. **易于扩展和维护**
   - 插件机制，支持自定义业务逻辑
   - 丰富的认证、监控、日志能力

---

## 二、迁移ToDo List

1. 评估现有Nginx配置，梳理所有流量入口、路由、限流、证书等配置
2. 部署Kong（推荐用Docker或Kubernetes）
3. 配置Upstream和Service，对接所有后端服务（Node.js、Polkadot等）
4. 配置Route，实现与现有Nginx一致的路由规则
5. 配置TLS证书（上传certbot证书或用ACME插件自动化）
6. 启用并配置IP Restriction插件，实现白名单/黑名单
7. 开发或定制限流插件，实现按方法权重计量和限流
8. 配置认证、监控、日志等插件（如有需要）
9. 测试所有功能，确保兼容性和性能
10. 切换流量到Kong，逐步替换Nginx
11. 持续监控与优化

---

## 三、参考文档链接

- [Kong 官方文档](https://docs.konghq.com/)
- [Kong IP Restriction 插件](https://docs.konghq.com/hub/kong-inc/ip-restriction/)
- [Kong ACME 插件（Let’s Encrypt自动化）](https://github.com/jeremyjpj0916/kong-plugin-acme)
- [Kong Rate Limiting 插件](https://docs.konghq.com/hub/kong-inc/rate-limiting/)
- [Kong 插件开发指南](https://docs.konghq.com/gateway/latest/plugin-development/)
- [OnFinality Response Units 说明](https://documentation.onfinality.io/support/response-units)
- [Alchemy Compute Units 说明](https://www.alchemy.com/docs/reference/compute-units)


Nginx 迁移到 Kong 的过程中，有一些业界公认的最佳实践，可以帮助你平滑、安全、高效地完成迁移，最大程度减少服务中断和风险。以下是详细建议：

---

## Nginx 迁移 Kong 最佳实践

### 1. 逐步迁移，分阶段切换

- **灰度发布**：先将部分流量导入 Kong，逐步扩大比例，观察稳定性和兼容性。
- **双活运行**：Nginx 和 Kong 并行运行一段时间，确保所有功能在 Kong 上表现正常后再完全切换。

### 2. 配置对比与自动化

- **配置梳理**：详细梳理 Nginx 的所有 location、rewrite、限流、证书、header、鉴权等配置，逐项映射到 Kong 的 Service、Route、Plugin。
- **自动化脚本**：用脚本或配置管理工具（如 Ansible、Terraform）自动化 Kong 配置，便于回滚和批量修改。

### 3. 插件与自定义逻辑

- **优先用官方/社区插件**：Kong 插件生态丰富，优先用现成插件实现限流、认证、IP控制等，减少自定义开发量。
- **自定义插件开发**：如需复杂的“按方法权重限流”，可用 Lua 开发自定义插件，建议先在测试环境充分验证。

### 4. 证书与安全

- **证书平滑迁移**：提前将 certbot 证书导入 Kong，或用 ACME 插件自动续期，避免证书失效导致服务中断。
- **安全策略同步**：确保 Nginx 上的安全策略（如 header、CORS、WAF）在 Kong 上有等效实现。

### 5. 监控与回滚

- **全链路监控**：接入 Prometheus、Grafana、ELK 等监控 Kong 的流量、延迟、错误率，及时发现问题。
- **回滚机制**：保留 Nginx 配置和环境，出现问题可快速切回 Nginx。

### 6. 性能与兼容性测试

- **压力测试**：上线前用工具（如 wrk、ab、locust）对 Kong 进行压力测试，确保性能达标。
- **兼容性测试**：覆盖所有业务场景，确保 Kong 的路由、限流、认证等行为与 Nginx 一致。

### 7. 文档与团队协作

- **详细文档**：记录迁移过程、配置映射、插件用法、常见问题，方便团队协作和后续维护。
- **团队培训**：让相关同事熟悉 Kong 的运维和插件开发。

---

## 参考文档

- [Kong 官方迁移指南](https://docs.konghq.com/gateway/latest/migration/)
- [Kong 插件开发指南](https://docs.konghq.com/gateway/latest/plugin-development/)
- [Kong 配置自动化（deck）](https://github.com/kong/deck)
- [Kong 生产环境最佳实践](https://docs.konghq.com/gateway/latest/production/)

---

总结：迁移时建议“分阶段、自动化、监控、可回滚”，优先用官方插件，复杂逻辑用自定义插件，做好测试和文档，确保平滑过渡。
