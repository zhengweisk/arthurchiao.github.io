---
layout    : post
title     : "[译] 一切系统，皆分布式（2003）"
date      : 2020-01-19
lastupdate: 2020-01-19
categories: distributed-system
---

### 译者序

本文内容来自 2016 年的一本免费电子书：[Everything is distributed]()。本
文翻译原书第二章 **Chapter 2: Everything is distributed**。如果以上链接
打不开，可以从这里下载：
[Free-OReilly-Books](https://vikaskyadav.github.io/Free-OReilly-Books/) 。

本文内容仅供学习交流，如有侵权立即删除。

**由于译者水平有限，本文不免存在遗漏或错误之处。如有疑问，请查阅原文。**

以下是译文。

----

<a name="ch_1"></a>


> 人们应该感到吃惊的并不是每天都有这么多故障，而是每天只有这么少故障。你不应该惊
> 讶于自己的系统偶尔会崩溃，而应该惊讶于它竟然能长时间不出错地运行。
> 
> — Richard Cook

2007 年 9 月，76 岁的 Jean Bookout 正在 Oklahoma 一条陌生的道路上驾驶着她的丰田凯美瑞
，她的朋友 Barbara Schwarz 坐在副驾驶的位置。突然，这辆汽车自己开始加速。
Bookout 尝试了踩刹车、拉手刹，但都不管用，汽车还在继续加速。最后这辆车撞上了路堤
，造成 Bookout 受伤，Schwarz 死亡。在随后的法律程序中，丰田的律师
pointed to the most common of culprits in these types of accidents: 认为错误（human
error）。“有时人们会在开车时犯错”，其中一位律师宣称。Bookout 年级很大了，而且也
不是她熟悉的道路，因此发生了这场悲剧。

然而，一个最近针对丰田的 [产品可靠性测试]() 结果
却为这件事故找到了一个非常不同的答案：凯美瑞中的一个软件 bug 导致的栈溢出错误（
stack overflow error）。这非常重要，因为两个原因：

1. 发生事故时经常被引用的罪魁祸首 —— 人为错误 —— 最后确认并不是这次事故的原因（
   该假设本身就不成立）
1. 这件事展示了我们如何跨越了一个阈值：从软件错误导致小烦恼或者（潜在的更大的）
   公司营收损失，到人身安全的领域。

要将这件事情解释为小故障可能也很容易：，
一个很普通的 bug （目前）似乎出现在了某款特定车型搭载的软件中。但这件事的外延要
有趣地多。考虑一下目前发现如火如荼的自动驾驶汽车。我们过滤掉那些事故中谣传的罪魁
祸首 —— 人为错误 —— 得到的结论将是，在很多方面，自动驾驶汽车比传统汽车更加安全。
但如果发生了完全在汽车的控制之外的事情，接下来将会怎么样？如果帮助汽车识别红灯的
输入数据流出错了怎么办？如果 Google 地图告诉它去做一些愚蠢的事情，并且这些事情很
危险怎么办？

We have reached a point in software development where we can no
longer understand, see, or control all the component parts, both technical
and social/organizational—they are increasingly complex and
distributed. The business of software itself has become a distributed,
complex system. How do we develop and manage systems that are too
large to understand, too complex to control, and that fail in unpredictable
ways?

# 1. Embracing Failure

Distributed systems once were the territory of computer science PhDs
and software architects tucked off in a corner somewhere. That’s no
longer the case. Just because you write code on a laptop and don’t have
to care about message passing and lockouts doesn’t mean you don’t
have to worry about distributed systems. How many API calls to
external services are you making? Is your code going to end up on
desktop sites and mobile devices—do you even know all the possible
devices? What do you know now about the network constraints that
may be present when your app is actually run? Do you know what your
bottlenecks will be at a certain level of scale?

One thing we know from classic distributed computing theory is that
distributed systems fail more often, and the failures often tend to be
partial in nature. Such failures are not just harder to diagnose and
predict; they’re likely to be not reproducible—a given third-party data
feed goes down or you get screwed by a router in a town you’ve never
even heard of before. You’re always fighting the intermittent failure,
so is this a losing battle?

The solution to grappling with complex distributed systems is not
simply more testing, or Agile processes. It’s not DevOps, or continuous
delivery. No one single thing or approach could prevent something
like the Toyota incident from happening again. In fact, it’s almost a
given that something like that will happen again. The answer is to
embrace that failures of an unthinkable variety are possible—a vast
sea of unknown unknowns—and to change how we think about the
systems we are building, not to mention the systems within which we
already operate.

# 2. Think Globally, Develop Locally

Okay, so anyone who writes or deploys software needs to think more
like a distributed systems engineer. But what does that even mean? In
reality, it boils down to moving past a single-computer mode of thinking.
Until very recently, we’ve been able to rely on a computer being a
relatively deterministic thing. You write code that runs on one machine,
you can make assumptions about what, say, the memory lookup
is. But nothing really runs on one computer any more—the cloud is
the computer now. It’s akin to a living system, something that is constantly
changing, especially as companies move toward continuous
delivery as the new normal.

So, you have to start by assuming the system in which your software
runs will fail. Then you need hypotheses about why and how, and ways
to collect data on those hypotheses. This isn’t just saying “we need more
testing,” however. The traditional nature of testing presumes you can
delineate all the cases that require testing, which is fundamentally impossible
in distributed systems. (That’s not to say that testing isn’t
important, but it isn’t a panacea, either.) When you’re in a distributed
environment and most of the failure modes are things you can’t predict
in advance and can’t test for, monitoring is the only way to understand
your application’s behavior.

# 3. Data Are the Lingua Franca of Distributed Systems

If we take the living-organism-as-complex-system metaphor a bit further,
it’s one thing to diagnose what caused a stroke after the fact versus
to catch it early in the process of happening. Sure, you can look at the
data retrospectively and see the signs were there, but what you want
is an early warning system, a way to see the failure as it’s starting, and
intervene as quickly as possible. Digging through averaged historical
time series data only tells you what went wrong, that one time. And in
dealing with distributed systems, you’ve got plenty more to worry
about than just pinging a server to see if it’s up. There’s been an explosion
in tools and technologies around measurement and monitoring,
and I’ll avoid getting into the weeds on that here, but what matters
is that, along with becoming intimately familiar with how histograms
are generally preferable to averages when it comes to looking
at your application and system data, developers can no longer think
of monitoring as purely the domain of the embattled system
administrator.

# 4. Humans in the Machine

There are no complex software systems without people. Any discussion
of distributed systems and managing complexity ultimately must
acknowledge the roles people play in the systems we design and run.
Humans are an integral part of the complex systems we create, and we
are largely responsible for both their variability and their resilience (or
lack thereof). As designers, builders, and operators of complex systems,
we are influenced by a risk-averse culture, whether we know it
or not. In trying to avoid failures (in processes, products, or large systems),
we have primarily leaned toward exhaustive requirements and
creating tight couplings in order to have “control,” but this often leads
to brittle systems that are in fact more prone to break or fail.
And when they do fail, we seek blame. We ruthlessly hunt down the
so-called “cause” of the failure—a process that is often, in reality, more
about assuaging psychological guilt and unease than uncovering why
things really happened the way they did and avoiding the same

outcome in the future. Such activities typically result in more controls,
engendering increased brittleness in the system. The reality is that
most large failures are the result of a string of micro-failures leading
up to the final event. There is no root cause. We’d do better to stop
looking for one, but trying to do so is fighting a steep uphill battle
against cultural expectations and strong, deeply ingrained psychological
instincts.

The processes and methodologies that worked adequately in the ’80s,
but were already crumbling in the ’90s, have completely collapsed.
We’re now exploring new territory, new models for building,
deploying, and maintaining software—and, indeed, organizations
themselves.

Photo by Mark Skipper, used under a Creative Commons license.

Learn More

Planning for failure
• Bloomberg on the Toyota acceleration case
• An analysis of the case from NHTSA
• The role of human error in the incident
• “Resilience In Complex Adaptive Systems: Operating At The
Edge Of Failure”, Velocity New York 2013 keynote by Richard
Cook
• “What, Where and When is the Risk in System Design?”, Velocity
Santa Clara 2013 Keynote by Johan Bergström
• Learning from First Responders: When Your Systems Have to
Work, free ebook by Dylan Richard
Managing complexity
• In Search of Certainty, by Mark Burgess
• “Beyond Automation with CFEngine 3”, video by Mark Burgess
• Continuous Quality (O’Reilly), by Jeff Sussna
• Building Anti-Fragile Systems and Teams (O’Reilly), by Dave
Zwieback
