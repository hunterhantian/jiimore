# 资料导航

先读 [研究报告](研究报告.md)，再看 [商品定向候选](商品定向候选表.md)。

本轮140次请求，24个独立父体、12组固定配对、48组关系查询、21个完整市场（13个复用+8个新增）、27个去重候选。没有执行广告。

raw保留全部请求响应与错误；requests.jsonl是独立账本；derived是完整明细。选择文件保留抽样规则。资料包保持目录结构，包含前轮research_20261108及三个复用根JSON。manifest.json提供SHA-256。

离线重算：在资料包根目录执行 `python3 asin_research_20261108/analyze.py`，再执行 `python3 asin_research_20261108/deliver.py`。这两个命令不发API请求。collect.py/client.py为采集实现，无需重跑。密钥仅从环境变量JIIMORE_TEST_KEY读取，未包含在文件中。

分类是标题规则；图像、实际广告、共同购买及历史库存均未核验。详情价格优先于市场接口，原始零值和矛盾保留。
