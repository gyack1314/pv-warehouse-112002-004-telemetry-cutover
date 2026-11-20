# 遥测切换期的双格式湖表与读者兼容

这是本地演练用的合成来源资料。CSV 使用 UTF-8、有首行字段名，空字段表示未提供。除非字段语义明确为零，空值没有数值含义。

## 资料契约

槽位为零基。vector_samples 每个槽位一行，用 block_id、sample_index、slot 定位。采样周期为秒，实际量值为 raw 乘以 10 的 scale10 次方，以伏计。质量掩码 1 表故障，2 表不可用，无位表示正常，其余位是新版专有含义。sample_state 可取 value、missing、clear。组完成清单中 expected_entries 以“块编号@采样序号@槽位”分号分隔，清单对给定组及半开窗口承诺完整覆盖，missing 不属于明确空槽。

以下文件位于 `data/`。

- `legacy_channels.csv`（5 条）：`wave`、`device`、`channel`、`local_at`、`centivolt`、`quality`
- `layouts.csv`（6 条）：`wave`、`layout`、`slot`、`channel`、`handover_group`
- `vector_blocks.csv`（3 条）：`wave`、`block_id`、`device`、`utc_start`、`step_seconds`、`layout`
- `vector_samples.csv`（12 条）：`wave`、`block_id`、`sample_index`、`slot`、`raw`、`scale10`、`quality_mask`、`sample_state`
- `cutovers.csv`（2 条）：`wave`、`device`、`handover_group`、`start`、`end`
- `completions.csv`（2 条）：`wave`、`device`、`handover_group`、`start`、`end`、`expected_entries`

`manifest.json` 记录完整资料包的列名、行数、校验值和跨表引用。完整资料包可含业务冲突或同一来源的多次投递，校验器保留这些原始记录。

## 本地查看

适用 Python 3.11，无需安装第三方库或构建。

```sh
python3 inspect_inputs.py
python3 -m unittest -v test_input_pack
```

查看命令打印各原始表的记录数。单元测试校验资料结构、引用与文件完整性，可离线执行。
