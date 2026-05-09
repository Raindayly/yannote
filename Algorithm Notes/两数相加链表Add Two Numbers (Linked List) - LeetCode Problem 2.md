链表两数相加 + 虚拟头节点 关键图解与注解
```markdown
## 核心注解（关键）
- dummy：虚拟头节点（占位，无实际数值，简化链表拼接逻辑）
- current：拼接指针，用于挂载新节点，避免直接操作dummy
- carry：进位（0或1），由Math.floor(sum/10)计算（向下取整）
- 核心逻辑：逆序链表逐位相加，进位向后传递，最终返回dummy.next（跳过占位节点）

---

## 1. 初始状态（关键图解）
```text
dummy(0) → null  // 虚拟头节点（占位）
↑
current        // 初始指向dummy，准备拼接新节点

l1: 2 → 4 → 3  // 代表342（逆序）
l2: 5 → 6 → 4  // 代表465（逆序）
carry = 0      // 初始无进位
```

---

## 2. 第一轮相加
注解：val1=2, val2=5, sum=7，无进位（carry=0），拼接节点7
```text
dummy(0) → 7 → null
           ↑
         current  // 后移，指向新拼接的节点

l1: 【2】→ 4 → 3  // 已计算的节点
l2: 【5】→ 6 → 4  // 已计算的节点
carry = 0
```

---

## 3. 第二轮相加
注解：val1=4, val2=6, sum=10，进位1（carry=1），拼接节点0
```text
dummy(0) → 7 → 0 → null
              ↑
            current

l1: 2 → 【4】→ 3
l2: 5 → 【6】→ 4
carry = 1
```

---

## 4. 第三轮相加
注解：val1=3, val2=4, sum=8（+进位1），无进位，拼接节点8
```text
dummy(0) → 7 → 0 → 8
                    ↑
                  current

l1: 2 → 4 → 【3】
l2: 5 → 6 → 【4】
carry = 0
```

---

## 5. 最终结果
 注解：返回dummy.next，跳过虚拟头节点，得到结果链表
```text
return dummy.next
结果链表：7 → 0 → 8  // 对应807（逆序）
```



``` ts
class ListNode {
    val: number
    next: ListNode | null
    constructor(val?: number, next?: ListNode | null) {
        this.val = (val === undefined ? 0 : val)
        this.next = (next === undefined ? null : next)
    }
}

function addTwoNumbers(l1: ListNode | null, l2: ListNode | null): ListNode | null {
    let dummy = new ListNode(0)
    let current = dummy
    let carry = 0

    let p1 = l1
    let p2 = l2
    while(p1 || p2 || carry) {
        const val1 = p1?p1.val:0
        const val2 = p2?p2.val:0
        let curSum = val1 + val2 + carry

        carry = Math.floor(curSum / 10)
        const digit = curSum % 10

        current.next = new ListNode(digit,null)

        current = current.next

        if(p1) p1 = p1.next
        if(p2) p2 = p2.next
    }
    return dummy.next
};

```
