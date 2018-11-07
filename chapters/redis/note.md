# 笔记

### 遍历命令
```
    keys  *user*
    keys  *
```

> 有3个通配符 *, ? ,[]

- *: 通配任意多个字符
- ?: 通配单个字符
- []: 通配括号内的某1个字符

注：生产已经禁止。更安全的做法是采用scan

- scan
- sscan 代替可能会阻塞服务器的 smembers 命令，遍历集合包含的各个元素
- hscan 代替可能会阻塞服务器的 hgetall 命令，遍历散列包含的各个键值对
- zscan 代替可能会阻塞服务器的 zrange 命令，遍历有序集合包含的各个元素

** 最大字符串为512M，但是大字符串非常不建议。 **

---

### SET 命令
SET 命令还支持可选的 NX 选项和 XX 选项，例如：SET nx-str "this will fail" XX
- 如果给定了 NX 选项，那么命令仅在键 key 不存在的情况下，才进行设置操作；如果键 key 已经存在，那么 SET ... NX 命令不做动作（不会覆盖旧值）。
- 如果给定了 XX 选项，那么命令仅在键 key 已经存在的情况下，才进行设置操作；如果键 key 不存在，那么 SET ... XX 命令不做动作（一定会覆盖旧值）。在给定 NX 选项和 XX 选项的情况下，SET 命令在设置成功时返回 OK ，设置失败时返回 nil 。

---

### getset 命令，配合 setnx 可实现分布式锁

```
    getset key value 
```

原子的设置key的值，并返回key的旧值。如果key不存在返回nil。应用场景：设置新值，返回旧值，配合setnx可实现分布式锁。

分布式锁的思路：注意该思路要保证多台Client服务器的NTP一致。

1. C3发送SETNX lock.foo 想要获得锁，由于C0还持有锁，所以Redis返回给C3一个0
2. C3发送GET lock.foo 以检查锁是否超时了，如果没超时，则等待或重试。
3. 反之，如果已超时，C3通过下面的操作来尝试获得锁：
4. GETSET lock.foo
5. 通过GETSET，C3拿到的时间戳如果仍然是超时的，那就说明，C3如愿以偿拿到锁了。
6. 如果在C3之前，有个叫C4的客户端比C3快一步执行了上面的操作，那么C3拿到的时间戳是个未超时的值，这时，C3没有如期获得锁，需要再次等待或重试。留意一下，尽管C3没拿到锁，但它改写了C4设置的锁的超时值，不过这一点非常微小的误差带来的影响可以忽略不计。

伪代码为：
```
    # get lock
    lock = 0
    while lock != 1:
        timestamp = current Unix time + lock timeout + 1
        lock = SETNX lock.foo timestamp
        if lock == 1 or (now() > (GET lock.foo) and now() > (GETSET lock.foo timestamp)):
            break;
        else:
            sleep(10ms)

    # do your job
    do_job()

    # release
    if now() < GET lock.foo:
        DEL lock.foo
```

---