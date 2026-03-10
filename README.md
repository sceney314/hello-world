### put 方法执行流程图

```mermaid
graph TD
    A[put(key, value)] --> B[putVal(key, value, false)]
    B --> C{参数校验}
    C -->|key/value为null| D[抛出NullPointerException]
    C -->|参数合法| E[计算hash值]
    E --> F[自旋循环开始]
    F --> G{table是否初始化?}
    G -->|未初始化| H[initTable()初始化]
    G -->|已初始化| I[计算桶位置i]
    H --> I
    I --> J{桶i是否为空?}
    J -->|为空| K[CAS无锁插入]
    K -->|CAS成功| L[跳出循环]
    K -->|CAS失败| F
    J -->|不为空| M{是否正在扩容?}
    M -->|是MOVED| N[helpTransfer协助扩容]
    N --> F
    M -->|否| O[synchronized锁定桶头节点]
    O --> P{再次检查桶头}
    P -->|被其他线程修改| F
    P -->|未修改| Q{数据结构类型}
    Q -->|链表fh>=0| R[遍历链表处理]
    Q -->|红黑树fh<0| S[putTreeVal处理]
    R --> T{找到相同key?}
    T -->|是| U[更新value值]
    T -->|否| V[链表末尾添加]
    S --> W{找到相同key?}
    W -->|是| X[更新value值]
    W -->|否| Y[插入树节点]
    U --> Z[检查树化条件]
    V --> Z
    X --> Z
    Y --> Z
    Z --> AA{binCount>=8?}
    AA -->|是| AB[treeifyBin树化]
    AA -->|否| AC[更新计数addCount]
    AB --> AC
    AC --> AD[返回结果]
    L --> AC
```


