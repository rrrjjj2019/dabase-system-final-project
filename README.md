<h1 align="center">
  <br>
  Database system final project
  <br>
</h1>


## Project Goal
Optimize database system's scalability with the TPC-C benchmark.

## Methodology
* Lock striping: 在 ConservativeLockTable 中， 需要 synchronized 的部分改以 Lock striping 的方式來實做。
* 縮小 Critical section 
* 在 CacheMgr 中，原本是每1秒檢查一次remote有沒有傳新的package，我們將其改為每 1ms 檢查一次。 
* Prefetching: 在 transaction 向系統索取 ConservativeLock 之前先進行 prefetching ，將transaction 會用到的資料讀入 cache，如此一來就能縮小 transaction 手上拿著lock的時間。
<br>
<p align="center">
<img src="https://github.com/rrrjjj2019/dabase-system-final-project/blob/master/prefetch.JPG" width="800" style="margin-right:5px; border: 1px solid #ccc;" />
</p>
<br>

* Early lock release: Transactions 原本是在 commit 時才會 release lock，而我們將 release lock 這個階段提早在cache flush 的過程中完成，由於我們仍然是在 資料 flush 後才 release lock，因此可以確定資料不會因此出錯。
<br>
<p align="center">
<img src="https://github.com/rrrjjj2019/dabase-system-final-project/blob/master/early_lock_release.JPG" width="800" style="margin-right:5px; border: 1px solid #ccc;" />
</p>
<br>

## Experiment
* Intel core i7-8700 CPU@3.20GHz   RAM: 8.0GB  Windows10
* TPCC-benchmark
* RTE = 30 / client
* 2 servers / computer,  4 computers
* 2 clients and 8 servers in total

## Result
* Optimized implementation
    - 233964 txns @ 8 servers, 斜率為 29,246 txns / server

* Without optimization
    - 160496 txns @ 8 servers 斜率為 20062 txns / server

* 45.7 % improvement @ 8 servers
<br>
<p align="center">
<img src="https://github.com/rrrjjj2019/dabase-system-final-project/blob/master/throughput.JPG" width="800" style="margin-right:5px; border: 1px solid #ccc;" />
</p>
<br>

<br>
