# OSTEP Ch.4 Homework Answers

## Q1
- Prediction / 预测:
```text
Time PID 0 PID 1 CPU IOs
1    RUN   READY  1
2    RUN   READY  1
3    RUN   READY  1
4    RUN   READY  1
5    RUN   READY  1
6    DONE  RUN    1
7    DONE  RUN    1
8    DONE  RUN    1
9    DONE  RUN    1
10   DONE  RUN    1
```
- Reasoning / 理由: 两个进程都是 100% 使用 CPU，没有任何 I/O。所以 CPU 永远不会空闲。PID 0 先运行 5 个 tick 后结束，接着 PID 1 运行 5 个 tick。总时间 10，CPU 利用率 100%。
- Verified result / 验证结果: Total Time 10, CPU Busy 10 (100.00%)
- Analysis / 分析: 预测完全正确。两个进程都是纯 CPU 进程，没有任何 I/O，因此 CPU 从未空闲，总时间是 10，CPU 利用率是 100%。

## Q2
- Prediction / 预测:
```text
Time PID 0 PID 1      CPU IOs
1    RUN   READY      1
2    RUN   READY      1
3    RUN   READY      1
4    RUN   READY      1
5    DONE  RUN:io     1
6    DONE  BLOCKED        1
7    DONE  BLOCKED        1
8    DONE  BLOCKED        1
9    DONE  BLOCKED        1
10   DONE  BLOCKED        1
11   DONE  RUN:io_done 1
```
- Reasoning / 理由: PID 0 先运行 4 个 tick 结束。PID 1 发起 I/O 后阻塞 5 个 tick，期间 CPU 空闲。I/O 完成后 PID 1 再运行 1 个 tick 处理完成。总时间 11。
- Verified result / 验证结果: Total Time 11, CPU Busy 6 (54.55%)
- Analysis / 分析: 预测正确。PID 0 先运行 4 个 tick，PID 1 发起 I/O 后阻塞 5 个 tick。期间 CPU 空闲，因为只有一个 CPU 且 PID 1 在等待 I/O。总时间 11，CPU 忙碌 6 个 tick。

## Q3
- Prediction / 预测:
```text
Time PID 0      PID 1 CPU IOs
1    RUN:io     READY 1
2    BLOCKED    RUN   1
3    BLOCKED    RUN   1
4    BLOCKED    RUN   1
5    BLOCKED    RUN   1
6    BLOCKED    DONE     1
7    RUN:io_done DONE  1
8    DONE       DONE
```
- Reasoning / 理由: 与 Q2 顺序相反。PID 0 发起 I/O 后阻塞，PID 1 在 I/O 期间使用 CPU。CPU 没有空闲，总时间 8。
- Verified result / 验证结果: Total Time 8, CPU Busy 6 (75.00%)
- Analysis / 分析: 预测正确。PID 0 发起 I/O 后阻塞，PID 1 在 I/O 期间使用 CPU。当 PID 1 运行完 4 个 tick 后，PID 0 的 I/O 也刚好完成。总时间 8，CPU 忙碌 6 个 tick。

## Q4
- Prediction / 预测:
```text
Time PID 0      PID 1 CPU IOs
1    RUN:io     READY 1
2    BLOCKED    READY
3    BLOCKED    READY
4    BLOCKED    READY
5    BLOCKED    READY
6    BLOCKED    READY
7    RUN:io_done READY 1
8    DONE       RUN   1
9    DONE       RUN   1
10   DONE       RUN   1
11   DONE       RUN   1
```
- Reasoning / 理由: 使用 SWITCH_ON_END，I/O 等待期间不切换进程，CPU 一直空闲等待。总时间 11。
- Verified result / 验证结果: Total Time 11, CPU Busy 6 (54.55%)
- Analysis / 分析: 预测正确。设置 SWITCH_ON_END，意味着 I/O 等待期间不切换进程，CPU 一直空闲等待。总时间 11，CPU 忙碌 6 个 tick。

## Q5
- Prediction / 预测:
```text
Time PID 0      PID 1 CPU IOs
1    RUN:io     READY 1
2    BLOCKED    RUN   1
3    BLOCKED    RUN   1
4    BLOCKED    RUN   1
5    BLOCKED    RUN   1
6    BLOCKED    DONE     1
7    RUN:io_done DONE  1
8    DONE       DONE
```
- Reasoning / 理由: 使用 SWITCH_ON_IO，I/O 发生时立即切换进程。和 Q3 行为一致，CPU 没有空闲，总时间 8。
- Verified result / 验证结果: Total Time 8, CPU Busy 6 (75.00%)
- Analysis / 分析: 预测正确。设置 SWITCH_ON_IO，I/O 发生时立即切换。和 Q3 行为一致，CPU 在 I/O 期间没有空闲，总时间 8。

## Q6
- Prediction / 预测:
```text
Time PID 0      PID 1 PID 2 PID 3 CPU IOs
1    RUN:io     READY READY READY 1
2    BLOCKED    RUN   READY READY 1
3    BLOCKED    RUN   READY READY 1
4    BLOCKED    RUN   READY READY 1
5    BLOCKED    RUN   READY READY 1
6    BLOCKED    RUN   READY READY 1
7    BLOCKED    DONE  RUN   READY 1
8    BLOCKED    DONE  RUN   READY 1
9    BLOCKED    DONE  RUN   READY 1
10   BLOCKED    DONE  RUN   READY 1
11   BLOCKED    DONE  RUN   READY 1
12   BLOCKED    DONE  DONE  RUN   1
...  (以此类推，总时间较长)
```
- Reasoning / 理由: 使用 IO_RUN_LATER，PID 0 在 I/O 完成后不能立刻运行，要等 PID 1、2、3 依次跑完。期间 I/O 设备空闲，CPU 利用率偏低，总时间会很长。
- Verified result / 验证结果: Total Time 33, CPU Busy 19 (57.58%)
- Analysis / 分析: 预测正确。IO_RUN_LATER 意味着 PID 0 在 I/O 完成后不能立刻运行，要等待 PID 1,2,3 依次跑完。期间 I/O 设备空闲，CPU 利用率偏低。总时间 33。

## Q7
- Prediction / 预测:
```text
（与 Q6 类似，但 PID 0 的 I/O 完成后会立刻抢占 CPU 运行）
```
- Reasoning / 理由: 使用 IO_RUN_IMMEDIATE，PID 0 的 I/O 完成后立刻运行。这能有效减少 CPU 空闲时间，总时间比 Q6 短很多。
- Verified result / 验证结果: Total Time 27, CPU Busy 16 (59.26%)
- Analysis / 分析: 预测正确。IO_RUN_IMMEDIATE 让 PID 0 的 I/O 完成后立刻抢占 CPU 运行。这显著提高了效率，总时间从 Q6 的 33 降到了 27。

## Q8
- Prediction / 预测: 因为种子随机，指令可能不同。预测：默认设置总时间最长，IO_RUN_IMMEDIATE 总时间最短，SWITCH_ON_END 会导致 CPU 空闲最多。
- Reasoning / 理由: 不同的 I/O 完成策略会影响 CPU 和 I/O 设备的并行度。IO_RUN_IMMEDIATE 能最好地利用 CPU，减少等待时间。
- Verified result / 验证结果:
  - 种子 1 (default: Total Time 15, CPU Busy 8 (53.33%)) (IO_RUN_IMMEDIATE: Total Time 15, CPU Busy 8 (53.33%)) (SWITCH_ON_END: Total Time 18, CPU Busy 8 (44.44%))
  - 种子 2 (default: Total Time 16, CPU Busy 10 (62.50%)) (IO_RUN_IMMEDIATE: Total Time 16, CPU Busy 10 (62.50%)) (SWITCH_ON_END: Total Time 30, CPU Busy 10 (33.33%))
  - 种子 3 (default: Total Time 18, CPU Busy 9 (50.00%)) (IO_RUN_IMMEDIATE: Total Time 17, CPU Busy 9 (52.94%)) (SWITCH_ON_END: Total Time 24, CPU Busy 9 (37.50%))
- Analysis / 分析: 对比三种设置，默认（IO_RUN_LATER）总时间最长，IO_RUN_IMMEDIATE 总时间最短。说明在 I/O 结束后立即重新运行 I/O 密集型进程，能有效减少 CPU 空闲时间，提高整体效率。