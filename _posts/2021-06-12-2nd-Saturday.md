---
layout: post
title: Weekly update - transfer queueing, execution safety flag, and MTP
date:  2021-06-12 10:10:00 +0900
categories: [DEV, GSOC_2021]
tags: [gsoc_2021, xfce]
---

## Transfer queueing

[Thunar !569](https://gitlab.xfce.org/xfce/thunar/-/issues/569)

After transfer queueing was introduced, it frequently caused crashes or freezing when working with remote location. This problem was solved by introducing a waiting queue. To be released in Thunar 4.17.4.

* This patch will not be backported. If you have this problem with Thunar 4.16, it is recommended to set parallel transfer to "Always" to avoid this problem.

* You might ask about how it was done before. How can a job can be queued when there is no queue? The answer is simple. Since every transfer job runs on a separate thread, it is possible to play a game of musical chairs. Every thread tries to be the only active thread, and if a thread fails to start, it waits until the next opportunity arises. This is easy to implement when parallel transfer is already implemented. But unfortunately, the game did not go well.

## WIP: Introduction of an execution safety flag

(I recommend reading [Thunar !156](https://gitlab.xfce.org/xfce/thunar/-/issues/156) for the context.)

Since the executable bit is not enough to confirm that the file is safe to execute,  I am implementing an additional metadata (execution safety flag). Thunar will check this flag even if the execution bit is on, so a user has to confirm that they know that they are launching an executable. This data is contained in GVFS-metadata, and will remember SHA-256 hash value of that file. This will work as a per-file option to allow launching the executable.

I already finished coding a feature to save a hash value per-file and using it to verify executable, and will be added to libxfce4util soon. Interface with this safety flag is still work-in-progress, though.

## WIP: MTP freeze

[Thunar:async-icon-render](https://gitlab.xfce.org/MShrimp4/thunar/-/commits/async-icon-render)

Recently, there are a lot of issue reports about MTP, and most of the complaints are about freeze. A directory under mtp:// that contains a lot of files tends to cause problems. One problem is that icon renderer reads file contents and might clog the main event loop, so I am experimenting with icon rendering right now. Trying to change directory while a directory is loading also freezes the environment, so requesting a file list of the current directory (or cancelling it) might be blocking for some reason.

