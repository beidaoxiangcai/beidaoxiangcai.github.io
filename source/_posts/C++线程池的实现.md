---
title: 'C++ 线程池的实现'
date: 2022-10-23 22:45:23
tags: C++
index_img: /img/page/cpp.png
---


# C++ 线程池的实现

+ 在并发编程中，使用多线程可以提高程序的执行效率。然而，手动管理线程可能会导致代码复杂和出错的风险，因此可以使用线程池来简化线程管理；
+ 线程池通过预先创建一定数量的线程，将任务分配给这些线程执行，从而提高程序的并发性和响应速度；
+ 线程池包括一个任务队列和一组工作线程，任务队列用于存储待执行的任务，工作线程则从任务队列中取出任务并执行。

## 任务队列实现 🔎
 任务队列要同时从多个用户中接收用于待执行的任务，因此要保证线程安全。这里可以使用STL中的队列或链表来实现，同时用一个互斥量来保证队列的线程安全，并使用条件变量来实现阻塞式的队列操作。任务队列的代码实现如下：

 线程安全是通过 std::mutex 和 std::condition_variable 实现的。其中，push() 函数用于向队列中添加元素，try_pop() 函数用于尝试弹出队首元素，wait_and_pop() 函数用于等待队列非空并弹出队首元素。

```cpp
#include <queue>
#include <mutex>
#include <condition_variable>

template<typename T>
class ThreadSafeQueue {
public:
    ThreadSafeQueue() = default;

    void push(const T& value) {
        std::lock_guard<std::mutex> lock(m_mutex);
        m_queue.push(value);
        m_cv.notify_one();
    }

    void push(T&& value) {
        std::lock_guard<std::mutex> lock(m_mutex);
        m_queue.push(std::move(value));
        m_cv.notify_one();
    }

    bool try_pop(T& value) {
        std::lock_guard<std::mutex> lock(m_mutex);
        if (m_queue.empty()) {
            return false;
        }

        value = std::move(m_queue.front());
        m_queue.pop();
        return true;
    }

    void wait_and_pop(T& value) {
        std::unique_lock<std::mutex> lock(m_mutex);
        m_cv.wait(lock, [this] { return !m_queue.empty(); });

        value = std::move(m_queue.front());
        m_queue.pop();
    }

    bool empty() const {
        std::lock_guard<std::mutex> lock(m_mutex);
        return m_queue.empty();
    }

private:
    std::queue<T> m_queue;
    mutable std::mutex m_mutex;
    std::condition_variable m_cv;
};

```



## 工作线程实现

工作线程用于从不停任务队列中取任务并执行。这里定义了一个 WorkerThread 类，它的 operator() 函数不断地从任务队列中取出任务并执行，直到线程停止。


```cpp
#include <thread>
#include <atomic>

class WorkerThread {
public:
    WorkerThread(ThreadSafeQueue<std::function<void()>>& taskQueue, std::atomic_bool& running)
        : m_taskQueue(taskQueue), m_running(running) {}

    void operator()() {
        while (m_running) {
            std::function<void()> task;
            if (m_taskQueue.try_pop(task)) {
                task();
            }
            else {
                std::this_thread::yield();
            }
        }
    }

private:
    ThreadSafeQueue<std::function<void()>>& m_taskQueue;
    std::atomic_bool& m_running;
};

```


## 线程池实现

这里定义了一个线程池类，为用户管理了任务队列和工作线程，线程线程池。


```cpp
#include <vector>

class ThreadPool {
public:
    ThreadPool(size_t numThreads = std::thread::hardware_concurrency())
        : m_running(true) {
        for (size_t i = 0; i < numThreads; ++i) {
            m_threads.emplace_back(WorkerThread(m_taskQueue, m_running));
        }
    }

    ~ThreadPool() {
        m_running = false;

        for (auto& thread : m_threads) {
            thread.join();
        }
    }

    template<typename Func, typename... Args>
    void submit(Func&& func, Args&&... args) {
        auto task = std::bind(std::forward<Func>(func), std::forward<Args>(args)...);
        m_taskQueue.push(task);
    }

private:
    void threadWorker() {
        while (m_running) {
            std::function<void()> task;
            if (m_taskQueue.try_pop(task)) {
                task();
            } else {
                std::this_thread::yield();
            }
        }
    }

    ThreadSafeQueue<std::function<void()>> m_taskQueue;
    std::vector<std::thread> m_threads;
    std::atomic<bool> m_running;
};

```

## 使用示例



```cpp
#include <iostream>
#include <chrono>

void task(int num) {
    std::cout << "Task " << num << " started" << std::endl;
    std::this_thread::sleep_for(std::chrono::seconds(1));
    std::cout << "Task " << num << " finished" << std::endl;
}

int main() {
    ThreadPool pool(4);  // 创建一个包含 4 个工作线程的线程池
    for (int i = 0; i < 8; ++i) {
        pool.submit(task, i);  // 向线程池中添加 8 个任务
    }
    return 0;
}

```

⚠ **Note**: 转载请注明出处