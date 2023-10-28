#### 一、功能实现

##### 1、sys_task_info系统调用

**功能描述：**通过发起sys_task_info系统调用，可以查询当前正在执行的任务信息，包括当前任务的状态、任务当前使用的系统调用次数、系统调用时刻距离任务第一次被调度时刻的时长（单位ms）。

**实现流程：**

	- 首先，扩展 TaskControlBlock 的相关信息，这里添加 syscall_times，start_time，started 等字段，其中 syscall_times 是一个数组，用于记录任务当前使用的系统调用次数，start_time用于记录sys_task_info 系统调用时的时刻，started 用来判断当前任务是否被启动； （在os/src/task/task.rs 中实现）
	- 其次，根据新的 TaskControlBlock，重写 TASK_MANAGER 的初始化构造过程以及一些方法(impl)，这里包括 run_first_task()、run_next_task();（在os/src/task/mod.rs 中实现）
	- 为了实现任务当前使用的系统调用次数功能，这里在进入内核态系统调用异常处理函数之后，进入具体系统调用函数之前添加 add_syscall_time() 函数，该函数具体实现可在 TASK_MANAGER 的方法中实现 。（在os/src/syscall/mod.rs 中实现）
	- 类似，在 TASK_MANAGER 的方法中实现 get_current_task_info() 函数，此函数用来获取当前任务的相关信息，并且将函数接口暴露给外界调用；（在os/src/task/mod.rs 中实现）
	- 最后，在 syscall 系统调用模块，添加新的系统调用 sys_task_info，该系统调用传入 TaskInfo 结构体，在函数中，通过调用 get_current_task_info() 实现对 TaskInfo 的构造。（在os/src/syscall/process.rs 中实现）

#### 二、问答题
​	   TODO...

**荣誉准则**

1. 在完成本次实验的过程（含此前学习的过程）中，我曾分别与 **以下各位** 就（与本次实验相关的）以下方面做过交流，还在代码中对应的位置以注释形式记录了具体的交流对象及内容：

   > *暂无*

2. 此外，我也参考了 **以下资料** ，还在代码中对应的位置以注释形式记录了具体的参考来源及内容：

   > *《rCore-Tutorial-Book 第三版》*
   >
   > *《RISC-V手册》*
   >
   > *《Operating Systems: Three Easy Pieces》*

3. 我独立完成了本次实验除以上方面之外的所有工作，包括代码与文档。 我清楚地知道，从以上方面获得的信息在一定程度上降低了实验难度，可能会影响起评分。

4. 我从未使用过他人的代码，不管是原封不动地复制，还是经过了某些等价转换。 我未曾也不会向他人（含此后各届同学）复制或公开我的实验代码，我有义务妥善保管好它们。 我提交至本实验的评测系统的代码，均无意于破坏或妨碍任何计算机系统的正常运转。 我清楚地知道，以上情况均为本课程纪律所禁止，若违反，对应的实验成绩将按“-100”分计。