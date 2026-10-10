# Context Engineering for AI Agents with LangChain and Manus（中英文对照）

---

**文件名：videoplayback \(1\)\.txt**

**时间：2026\-10\-10 15:37:15 CST \| 1h 52s**

**Keywords / 关键词：**

- agents tools / 智能体工具

- model context / 模型上下文

- different agents / 不同智能体

- search tools / 搜索工具

- different models / 不同模型

- new tool / 新工具

- manager agent / 管理器智能体

- Code Agents / 代码智能体

- few agent / 少量智能体

- sub agent / 子智能体

- tool outputs / 工具输出

- multi agents / 多智能体

- new model / 新模型

- full context / 完整上下文

- designer agent / 设计师智能体

- knowledge agent / 知识智能体

- tool messages / 工具消息

- planning agent / 规划智能体

    

---



**Speaker 1 00:06**



EN: Alright, well, thank you all for coming\. We'll go ahead and kick off the webinar now\. And I'm sure people continue to stream in\. I'm Lance, one of the founding engineers at LangChain, and joined by Peak from Manus\. Do you wanna introduce yourself quickly?



ZH: 好的，感谢大家前来参加。我们现在就开始这场网络研讨会。我相信还有人会陆续加入。我是 Lance，是 LangChain 的创始工程师之一。今天和我一起的是来自 Manus 的 Peak。你要不要快速介绍一下自己？



---



**Speaker 2 00:26**



EN: Yeah, hey guys, I'm the co\-founder and chief scientist of Manus\. So basically I designed the agent framework and a lot of things in Manus\. And I'm super excited to be here today\. Thanks guys for having me\.



ZH: 是的，大家好，我是 Manus 的联合创始人兼首席科学家。基本上，我设计了 Manus 的智能体框架以及很多其他东西。今天非常高兴来到这里。感谢大家邀请我。



---



**Speaker 1 00:36**



EN: Yeah, we're really excited to do this because Manus is a really cool product\. I've been using it for a long time\. But also they put out a really nice blog post on context engineering a few months ago that influenced me a lot\. So I wanna give a quick overview of context engineering as I see it, and I'll reference their piece\. And then Peak is actually gonna give a presentation, talk about some new ideas not covered in the piece\. So if you've already read it, he can just cover some things that are new, which hopefully will be quite interesting for you\. But I'll still set the stage and I'll hand it over to Peak, and then we'll do some Q\&A\.



ZH: 是的，我们非常兴奋能做这场活动，因为 Manus 是一个很酷的产品。我已经用了很久。而且几个月前他们还发布了一篇关于上下文工程的很好的博客文章，对我影响很大。所以我想先按我的理解快速概述一下上下文工程，并引用他们的文章。然后 Peak 实际上会做一个演讲，谈一些文章中没有覆盖的新想法。所以如果你已经读过那篇文章，他可以只讲一些新的内容，希望对你会很有趣。不过我还是会先铺垫一下，然后把时间交给 Peak，之后我们做一些问答。



---



**Speaker 1 01:10**



EN: So you might have heard this term context engineering, and it kind of emerged earlier this year\. If you look through time with Google Search Trends, context engineering was kind of initiated following ChatGPT\. So that's showing December 2022\. And we got this new thing, a chat model\. There became a great deal of interest in how we prompt these things, prompt engineering kind of emerges as a discipline for working with chat models and prompting them\. Now context engineering emerged, can it this year around May, we saw it really rising in Google Trends\. And it corresponds a bit with this idea of the year of agents\. And so why is that?



ZH: 你可能听说过“上下文工程”这个术语，它大概是在今年早些时候出现的。如果你看 Google 搜索趋势，上下文工程是在 ChatGPT 之后开始被引发的。图上显示的是 2022 年 12 月。我们有了这个新东西——聊天模型。于是人们对如何提示这些东西产生了巨大兴趣，提示工程作为一门与聊天模型协作并提示它们的学科出现了。现在上下文工程出现了，今年大概五月，我们看到它在 Google Trends 中真正上升。它和“智能体之年”这个想法有些对应。那么为什么呢？



---



**Speaker 1 01:53**



EN: One of the things that people have observed if you've been building agents is that context grows\. And it grows in a very particular way\. When you build an agent, what it means is we have an LM bound to some number of tools that LM can call tools autonomously in a loop\. The challenge is for every tool call, you get a tool observation back and that's appended to this chat list\. These messages grow over time\. And so you can kind of get this unbounded explosion of messages as agents run\. As an example, Manus talked on their piece that typical tasks require around 50 tool calls\.



ZH: 如果你一直在构建智能体，你会观察到的一件事是：上下文会增长。而且它以非常特殊的方式增长。当你构建一个智能体时，意味着我们有一个语言模型绑定到若干工具，模型可以在循环中自主调用工具。挑战在于，每次工具调用，你都会得到一个工具观察结果，它会被追加到这个聊天列表中。这些消息会随时间增长。所以当智能体运行时，你会得到这种无界爆炸式增长的消息。举个例子，Manus 在他们的文章中提到，典型任务大约需要 50 次工具调用。



---



**Speaker 1 02:32**



EN: Anthropic mentioned similarly that production agents can engage in conversations spending hundreds of turns\. So the challenge is that agents, because they're increasingly long running and autonomous, they utilize tools freely\. You can accumulate a large amount of context through this accumulation of tool calls\. And Chroma will put out a really nice report talking about context rot\. The observation simply is that performance drops as context grows\. See this paradox? See this challenging situation? Agents utilize lots of context because of tool calling, but we know that performance drops as context grows\. So this is a challenge that many of us have faced, and it kind of spearheaded this, or I think seeded this term of context engineering\. Karpathy kind of coined it on Twitter earlier this year\. Anything about context engineering is a delicate art science of filling the context window with just the right information needed for the next step\.



ZH: Anthropic 也提到类似的情况：生产环境中的智能体可以参与对话，花费数百轮。所以挑战在于，智能体越来越长时间运行、越来越自主，它们会自由使用工具。通过工具调用的积累，你可以累积大量上下文。Chroma 发布了一份很好的报告，讨论“上下文腐化”。观察很简单：随着上下文增长，性能会下降。看到这个悖论了吗？看到这个挑战性局面了吗？智能体因为工具调用而使用大量上下文，但我们知道上下文增长会降低性能。所以这是我们许多人都面临过的挑战，它也推动了，或者我认为催生了“上下文工程”这个术语。Karpathy 今年早些时候在 Twitter 上创造了这个词。关于上下文工程，可以说是一门精细的艺术与科学：用下一步所需的恰好正确的信息填满上下文窗口。



---



**Speaker 1 03:45**



EN: So trying to combat this context explosion that happens, we build agents and they call tools freely\. All those tool messages accumulating in your message queue\. How do we kind of call such that the right information is presented to the agent to make the correct next decision at all points in time\.



ZH: 所以为了对抗这种上下文爆炸：我们构建智能体，它们自由调用工具。所有这些工具消息都会累积在你的消息队列中。我们如何调用，才能让智能体在任何时间点都获得正确的信息，从而做出正确的下一步决策？



---



**Speaker 1 03:45**



EN: So to address this, there's a few common themes I wanna highlight that we've seen across a number of different pieces of work, including Manus, which I'll mention here\. Idea one is context offloading\. So we've seen this trend over and over\. Central idea is you don't need all context to live in this messages history of your agent\. You can take information and offload it, send it somewhere else\. So it's outside the context window, but it can be retrieved, which we'll talk about later\. So one of the most popular ideas here is just using a file system\. Take the output of a tool message as an example, dump it to the file system, send back to your agent just some minimal piece of information necessary so it can reference the full context if it needs to\. But that full payload, for example, web search result that's very token heavy isn't spammed into your context window for perpetuity\. So you've seen this across a number of different projects\. Manus uses this\. We have a project called Deep Agents that utilizes the file system\. Open Deep Research utilizes actually Agent State as a similar role to external file system\. Claude Code, of course, uses this very extensively\. Long running agents utilize it very extensively\. So this idea of offloading context to a file system is very common and popular across many different examples of production agents that we're seeing today\.



ZH: 为了解决这个问题，我想强调几个常见主题，这些主题我们在许多不同的工作中都见过，包括 Manus，我这里会提到。第一个想法是上下文卸载。我们反复看到这个趋势。核心思想是：你不需要所有上下文都活在智能体的消息历史中。你可以把信息卸载、发送到其他地方。这样它就在上下文窗口之外，但仍然可以被检索，我们稍后会讲。这里最流行的想法之一就是使用文件系统。以工具消息的输出为例，把它转储到文件系统，只发回给智能体最小必要信息，这样如果需要，它可以引用完整上下文。但完整负载，比如非常耗 token 的网页搜索结果，就不会永久塞进你的上下文窗口。所以你在很多项目中都见过这一点。Manus 用了这个。我们有一个叫 Deep Agents 的项目，利用了文件系统。Open Deep Research 实际上利用 Agent State 扮演类似外部文件系统的角色。Claude Code 当然非常广泛地使用它。长时间运行的智能体也非常广泛地使用它。所以把上下文卸载到文件系统这个想法，在今天我们看到的许多生产智能体例子中都非常常见和流行。



---



**Speaker 1 05:13**



EN: The second idea is reducing context\. So offloading is very simply taking some piece of information like a tool message that's token heavy and not sending it all back to your messages list, dumping it to a file system where it can be retrieved only as needed\. That's offloading\. Reducing the context is similar, but instead you're just summarizing or compressing information\. Summarizing tool call outputs is one intuitive way to do this\. So we do this with Open Deep Research as an example, pruning tool calls or tool messages\.



ZH: 第二个想法是缩减上下文。卸载很简单：拿一段信息，比如一个 token 很重的工具消息，不要把它全部发回消息列表，而是转储到文件系统，只有需要时才检索。那就是卸载。缩减上下文类似，但相反，你只是总结或压缩信息。总结工具调用输出是一种直观做法。比如我们在 Open Deep Research 中就这么做，剪除工具调用或工具消息。



---



**Speaker 1 05:48**



EN: One thing that's very interesting is Claude 4\.5 has actually added this to some of their most recent releases\. They now support this out of the box\. So this idea, pruning old tool calls with tool outputs or tool messages, it's something that Claude is now kind of built into their SDK\.



ZH: 很有意思的一点是，Claude 4\.5 实际上在最近一些发布中加入了这一点。它们现在开箱即用支持这个。所以这个想法——剪除旧的工具调用及其工具输出或工具消息——Claude 现在某种程度上已经把它内置到 SDK 中了。



---



**Speaker 1 06:07**



EN: Summarize your compacting full message history\. You see this with Claude Code in its compaction feature\. Once you hit a certain percentage of your overall context window, Cognition also talks about they have summarization at agent\-to\-agent handoffs\. So this idea of reducing context is a very popular theme we see across a lot of different examples from Claude Code to our Open Deep Research to Cognition\. Claude 4\.5 has incorporated this as well\.



ZH: 总结或压缩完整消息历史。你在 Claude Code 的压缩功能中看到这一点。一旦达到整体上下文窗口的某个百分比，就会触发。Cognition 也谈到他们在智能体到智能体交接时进行总结。所以缩减上下文这个想法，是我们在很多不同例子中都看到的非常流行的主题，从 Claude Code 到我们的 Open Deep Research，再到 Cognition。Claude 4\.5 也已经加入了这一点。



---



**Speaker 1 06:32**



EN: Retrieving context\. Now this is one of the classic debates today that you might see raging on X or Twitter\. The right approach for retrieving context\. Lee Robinson from Cursor just had a very nice talk and I'll make sure these slides are all shared so you can see these links\. He had a very nice talk at OpenAI DevDay\. Talking about Cursor search, for example, uses indexing and semantic search as well as more simple file\-based search tools like grep and glob\. Claude Code of course only uses the file system and simple search tools, notably glob and grep\. So there's different ways to retrieve context on demand for your agent\. Indexing and something like semantic search, file system and simple file search tools, both can be highly effective\. There's pros and cons we could talk about in the Q\&A, but of course, context retrieval is central for building effective agents\.



ZH: 检索上下文。这是今天你可能会在 X 或 Twitter 上看到激烈争论的经典话题之一：检索上下文的正确方式。Cursor 的 Lee Robinson 刚做了一个很好的演讲，我会确保分享这些幻灯片，这样你可以看到链接。他在 OpenAI DevDay 上有一个很好的演讲。比如谈到 Cursor 搜索使用索引和语义搜索，也使用更简单的基于文件的搜索工具，如 grep 和 glob。Claude Code 当然只使用文件系统和简单搜索工具，尤其是 glob 和 grep。所以为智能体按需检索上下文有不同的方式。索引和语义搜索、文件系统和简单文件搜索工具，两者都可以非常有效。我们可以在问答中讨论优缺点，但当然，上下文检索是构建有效智能体的核心。



---



**Speaker 1 07:24**



EN: Context isolation is the other major theme we've seen quite a bit of, in particular, splitting context across multi agents\. So what's the point here? Each sub agent has its own context window and some agents lack for separation of concerns\. Manus Wide Agents talks about this\. Our Deep Agents work uses this\. Open Deep Research uses it\. Claude sub agents are utilized in their research and sub agents are utilized in Claude's research, planning agent researcher, and also Claude Code supports sub agents\. These are all, so sub agents are a very common way to perform context isolation\. We've seen across many different projects\.



ZH: 上下文隔离是我们见过的另一个主要主题，尤其是把上下文拆分到多个智能体中。这里的要点是什么？每个子智能体有自己的上下文窗口，有些智能体缺乏关注点分离。Manus 的 Wide Agents 谈到了这一点。我们的 Deep Agents 工作使用了这个。Open Deep Research 也使用它。Claude 的研究中使用了子智能体，Claude 的研究、规划智能体、研究员中也使用了子智能体，Claude Code 也支持子智能体。所以子智能体是执行上下文隔离的一种非常常见方式。我们在许多不同项目中都见过。



---



**Speaker 1 08:05**



EN: Now, one thing I thought was very interesting is caching context, and Manus mentioned this quite a bit\. I'll let Peak speak to this a bit later, but I think it's a very interesting trick as well\.



ZH: 现在，我觉得很有意思的一点是缓存上下文，Manus 也多次提到这个。稍后我会让 Peak 谈一谈，但我认为这也是一个非常有趣的技巧。



---



**Speaker 1 08:15**



EN: So I'll just show a brief example that we've seen across Open Deep Research\. This is a very popular repo that we have\. It's basically an open source deep research implementation\. It performs on par with some of the best implementations out there\. You can check our repo and we have results from deep research benchmarks showing that we're top 10\. It has three phases: scoping of the research phase itself using a multi agent basically architecture, and then a final one\-shot writing phase\. We use offloading\. So we basically create a brief to scope our research plan\. We offload that\. So we don't just say that in the context window because that context window is gonna get peppered with other things\. We offload it so it's saved independently\. It can be accessed in our case from the LangGraph state, but it could also be from file system, same idea\.



ZH: 我会简要展示一个我们在 Open Deep Research 中看到的例子。这是我们一个非常受欢迎的仓库。它基本上是一个开源深度研究实现。它的表现与一些最好的实现相当。你可以查看我们的仓库，我们有深度研究基准测试结果，显示我们进入前十。它有三个阶段：用多智能体架构对研究阶段本身进行范围界定，然后最后是一次性写作阶段。我们使用卸载。所以我们基本上创建一个 brief 来界定研究计划。我们把它卸载。我们不只是把它放在上下文窗口里，因为那个上下文窗口会被其他东西不断填充。我们把它卸载，这样它会被独立保存。在我们的案例中可以从 LangGraph 状态访问，但也可以从文件系统访问，思路一样。



---



**Speaker 1 09:00**



EN: So you create a research plan, you offload it, this is always accessible\. You go do a bunch of work, you can pull that back in on demand\. So you can put it kind of at the end of your message list so it's accessible and readily available to your agent to form, for example, the writing phase\.



ZH: 所以你创建一个研究计划，把它卸载，它始终可访问。你去做一堆工作，然后可以按需把它拉回来。所以你可以把它放在消息列表末尾，这样智能体可以访问并随时使用它，比如用于形成写作阶段。



---



**Speaker 1 09:16**



EN: We use offloading, as you can see, to help steer the research and writing phases\. We use reduction to summarize observation from token heavy search tool calls that's done inside research itself\. And we use context isolation across sub agents within research itself\. And this is kind of a summary of a bunch of different of these various ideas across a bunch of different projects\. And actually, Peak is gonna speak to Manus in particular and some of the lessons they've learned\. This just kind of sets up the stage\. And this just kind of summarizes what I talked about, these different themes of offloading, reducing context, retrieving context, isolating, caching, and a number of popular projects and kind of where they're used and a few different links\. I will share these slides in the notes and I do wanna let Peak go ahead and present now, cuz I wanna make sure we have plenty of time for him and for questions\. But this sets the stage and Peak, let you take it from here\. I'll stop sharing\.



ZH: 如你所见，我们使用卸载来帮助引导研究和写作阶段。我们使用缩减来总结研究中 token 很重的搜索工具调用观察结果。我们在研究内部跨子智能体使用上下文隔离。这只是对许多不同项目中这些不同想法的一个总结。实际上，Peak 会特别讲 Manus 以及他们学到的一些经验。这里只是铺垫一下。这也总结了我谈到的不同主题：卸载、缩减上下文、检索上下文、隔离、缓存，以及一些流行项目和它们的使用场景，还有一些链接。我会把这些幻灯片分享到笔记中。我现在想让 Peak 开始演讲，因为我想确保他有充足时间，也有时间回答问题。但这已经铺好了舞台，Peak，接下来交给你。我停止共享。



---



**Speaker 2 10:25**



EN: Okay\. Can you see my slide?



ZH: 好的。你能看到我的幻灯片吗？



---



**Speaker 1 10:28**



EN: Yeah, okay\.



ZH: 是的，可以。



---



**Speaker 2 10:29**



EN: Perfect\. Okay, thank you, Lance\. I'm super excited to be here today to share some fresh lessons on context engineering that we learned from building Manus\. You know, here I say fresh lessons because I realize that the last blog post that you mention I wrote about Context Engineering was back in July\. And yeah, it's the year of the agent\. So July is basically the last entry\. And of course, before this session, I went back and read it again\. And luckily, I think like most of what I wrote in that blog still holds up today, but I just don't want to like waste everybody's time by like just repeating what's already inside that blog\. So today I think instead I want to dig into some areas that I either didn't go deep enough on before or didn't touch at all, so\.



ZH: 太好了。好的，谢谢你，Lance。今天非常兴奋能在这里分享一些我们从构建 Manus 中学到的关于上下文工程的新经验。我说“新经验”，因为我意识到你提到的那篇我写的关于上下文工程的博客是七月发布的。是的，这是智能体之年。所以七月基本上已经是上一篇文章了。当然，在这次分享之前，我又回去读了一遍。幸运的是，我认为那篇博客里的大部分内容今天仍然成立。但我不想浪费大家时间，只是重复博客里已经有的东西。所以今天我想深入一些之前没讲够、或者完全没触及的领域。



---



**Speaker 2 11:15**



EN: Actually, we'll be focusing on the discourage column in Lance's earlier slides because, you know, personally, I think exploring those non\-consensus ideas often leads to the biggest inspirations\. So here's a topic for today's talk\. First, we'll cover a bit about the bigger question of why we need context engineering\. And then we'll have more on context reduction, more on context isolation, and finally some like some new stuff about context offloading, which we're testing internally here at Manus\. Yeah, so everything I'm sharing today is in production and then it's battle tested\. But I don't know how long it will last because, you know, things are changing super fast\. Okay, let's start with the first big question\.



ZH: 实际上，我们会聚焦 Lance 之前幻灯片里的“不推荐”那一栏，因为个人而言，我认为探索那些非共识想法往往会带来最大的启发。所以这是今天演讲的主题。首先，我们会稍微讨论一个更大的问题：为什么我们需要上下文工程。然后我们会更多谈上下文缩减、上下文隔离，最后是一些关于上下文卸载的新东西，这些是我们在 Manus 内部正在测试的。是的，我今天分享的所有东西都在生产环境中，并且经过了实战检验。但我不知道它们能持续多久，因为你知道，事情变化非常快。好的，让我们从第一个大问题开始。



---



**Speaker 1 12:01**



EN: Why do we\.



ZH: 为什么我们——



---



**Speaker 2 12:02**



EN: Even need context engineering? Especially, you know, when fine tuning or post training models has become like much more accessible today\. Yeah, for example, folks at the Thinking Machine Team, they just release the Tinker API, which I like a lot\. Beautiful design\.



ZH: 甚至需要上下文工程？尤其是当今天微调或后训练模型已经变得更容易获得时。比如，Thinking Machines 团队刚发布了 Tinker API，我非常喜欢。设计很漂亮。



---



**Speaker 2 12:19**



EN: But for me, the question like why context engineering actually came through several painful stages of realization\. Before starting Manus, I've already spent over 10 years in natural language processing, or NLP, which is basically what we call building language models\. But before ChatGPT and Manus, actually my second or third company\. At my previous startup, we train our own language model from scratch to do open domain information extraction and building knowledge graph and semantic search engines on top of them\. And it was painful\. Our product's innovation speed was completely capped by the model's iteration speed\. You know, even back then, the models were much smaller comparing to today, but still a single training plus evaluation cycle could take maybe like one or two weeks\. And the worst part is that at that time, we hadn't reach PMF yet and we're spending all of that time like improving benchmark that might not even matter for the product\. So I think instead of building specialized models too early, startups really should lean on general models and context engineering for as long as possible\.



ZH: 但对我来说，“为什么需要上下文工程”这个问题，实际上经历了几个痛苦的醒悟阶段。在创办 Manus 之前，我已经在自然语言处理，也就是 NLP，基本上就是我们所说的构建语言模型领域，花了超过 10 年。但在 ChatGPT 和 Manus 之前，实际上这是我的第二或第三家公司。在我之前的创业公司，我们从零训练自己的语言模型，做开放域信息抽取，并在其上构建知识图谱和语义搜索引擎。那很痛苦。我们产品的创新速度完全被模型迭代速度限制。你知道，即使当时模型比今天小得多，但一次训练加评估周期可能也要一到两周。最糟糕的是，当时我们还没达到 PMF，却把所有时间花在改进可能对产品根本不重要的基准上。所以我认为，创业公司不应过早构建专用模型，而应尽可能长时间依赖通用模型和上下文工程。



---



**Speaker 2 13:29**



EN: Well, of course, I guess now that's some kind of common wisdom\. But as your product matures and open source base model gets stronger, I know it's very tempting to think, hey, maybe I should like just pick a strong base model, fine tune it with my data and make it really good at my use case\. You know, we've tried that too\. And guess what? It's another trap, you know, to make\.



ZH: 当然，我猜现在这已经算是某种常识了。但随着你的产品成熟，开源基础模型变得更强，我知道这很诱人：嘿，也许我应该挑一个强的基础模型，用我的数据微调它，让它非常适合我的用例。你知道，我们也试过。你猜怎么着？这是另一个陷阱。



---



**Speaker 1 13:51**



EN: A real work really\.



ZH: 让它真正有效。



---



**Speaker 2 13:52**



EN: Well, you usually fix an action space, the design of reward around your current product behavior and generate tons of like on policy rollouts and feedback\. But you know, this is also dangerous because we're still in the early days of AI and agents\. Everything can shift under feet overnight\.



ZH: 你通常会固定一个动作空间，围绕当前产品行为设计奖励，并生成大量 on\-policy rollout 和反馈。但你知道，这也很危险，因为我们仍处在 AI 和智能体的早期。一切可能一夜之间发生转变。



---



**Speaker 2 14:09**



EN: For us, the classic example was the launch of MCP\. Actually, it completely changed the design of Manus from a compact static action space to something like it's infinitely extensible\. And if you have ever trained your own model, you know that this kind of open domain problem is super hard to optimize\.



ZH: 对我们来说，经典例子是 MCP 的发布。实际上，它完全改变了 Manus 的设计，从一个紧凑的静态动作空间，变成某种无限可扩展的东西。如果你曾训练过自己的模型，你知道这种开放域问题超级难优化。



---



**Speaker 2 14:28**



EN: Well, of course, you could like pour massive effort into post training that ensures generalization, but then aren't you basically trying to become an LLM company yourself? Because you're basically rebuilding the same layer that they have already built, and that's a duplication of effort\. So maybe after all that build up, here's my point, be firm about where you draw the line\.



ZH: 当然，你可以投入巨大精力做后训练来确保泛化，但那样你不就基本上想自己变成一家大模型公司了吗？因为你基本上在重建他们已经构建好的同一层，这是重复劳动。所以也许铺垫了这么多，我的观点是：要坚定地划清边界。



---



**Speaker 2 14:51**



EN: Right now, context engineering is the clearest and most practical boundary between application and model\. So trust your choice\. Alright, enough philosophy and let's talk about some real tech\. First topic, context reduction\. Here I want to like clarify two different kinds of compaction operations because we think like context reduction is fascinating, but it's also a new concept\. There's a lot of way to do this\. And here at Manus, we divide them into compaction and summarization\.



ZH: 现在，上下文工程是应用和模型之间最清晰、最实用的边界。所以相信你的选择。好了，哲学够了，我们谈点真正的技术。第一个主题：上下文缩减。这里我想澄清两种不同的压缩操作，因为我们认为上下文缩减很迷人，但它也是一个新概念。做这件事有很多方式。在 Manus，我们把它分为压缩和总结。



---



**Speaker 2 15:21**



EN: For compaction, it matters every tool call and tool result\. We actually has two different formats, a full format and a compact one\. The compact version strips out any information that can be reconstructed from the file system or external state first\. For example, here, let's say you have a tool that writes to a file and it probably has two fields, a path and a content field\. And, but once the tool returns, you can ensure that the file already exists in the environment\. So in the compact format, we can safely drop the super long content field and just keep the path\. And if your agent is smart enough, well, like whenever it needs to read that file again, it can simply retrieve it via the path\. So no information is truly lost\. It's just like externalized\.



ZH: 对于压缩，重要的是每次工具调用和工具结果实际上有两种格式：完整格式和紧凑格式。紧凑版本会先剥离任何可以从文件系统或外部状态重建的信息。例如，假设你有一个写入文件的工具，它可能有两个字段：路径和内容。但工具返回后，你可以确保文件已经存在于环境中。所以在紧凑格式中，我们可以安全地丢掉超长的内容字段，只保留路径。如果你的智能体足够聪明，每当它需要再次读取那个文件时，只需通过路径检索即可。所以没有信息真正丢失，只是被外部化了。



---



**Speaker 2 16:09**



EN: We think this kind of like reversibility is crucial because agents do like chain predictions based on previous actions and observations\. And you never know like which past action will suddenly become super important like 10 steps later\. You cannot predict it\. So this is a reversible reduction by using compaction\. Of course, like compaction only takes you so far\. Eventually, like your context will still grow and will hit the ceiling\. And that's when we combine compaction with the more like traditional summarization, but we do it very carefully\. For example, here, before summarizing, we might offload key parts of the context into files\. And sometimes like we even more do more aggressively, we can dump the entire pre\-summary context as a text file or simply a log file into the file system so that we can like always recover it later\. And like Lance like just mention some people just use like globbing grep, you know, globally grep also works for log files\. So if the model is smart enough, it even knows how to retrieve those like summarized, those pre\-summarized context\.



ZH: 我们认为这种可逆性至关重要，因为智能体会基于之前的行动和观察进行链式预测。你永远不知道哪个过去的行动会在 10 步之后突然变得超级重要。你无法预测。所以通过压缩，我们实现了可逆的缩减。当然，压缩只能带你走这么远。最终你的上下文仍会增长，并撞到上限。这时我们会把压缩与更传统的总结结合起来，但我们非常小心。例如，在总结之前，我们可能把上下文的关键部分卸载到文件中。有时我们甚至更激进，可以把整个总结前的上下文作为文本文件或日志文件转储到文件系统，这样以后总能恢复。正如 Lance 提到的，有些人只用 glob 和 grep，你知道，全局 grep 对日志文件也有效。所以如果模型足够聪明，它甚至知道如何检索那些被总结、那些总结前的上下文。



---



**Speaker 2 17:13**



EN: So I think the difference here is that compaction is reversible, but summarization isn't\. Both reduce context length, but they behave very differently\. And to make these two methods coexist, we have to track some like context length thresholds at the top\. Like you'll have your model's hard context limit, say like 1 million tokens pretty common today\. But you know, in reality, most models start degrading much earlier, typically maybe around 200k\. And you begin to see what we call a context rot\. It's like repetitions, slower inferences, degraded quality\. So by doing a lot of the evaluation, it's very important for you to identify that rot threshold\. It's typically 128k to 200k, and use it as the trigger for context reduction\. And whenever like your context size approaches it, you have to trigger a context reduction\.



ZH: 所以我认为区别在于：压缩是可逆的，但总结不是。两者都减少上下文长度，但行为非常不同。为了让这两种方法共存，我们必须在顶层跟踪一些上下文长度阈值。比如模型有硬性上下文限制，今天 100 万 token 很常见。但实际上，大多数模型会远早于这个限制开始退化，通常可能在 200k 左右。你会开始看到我们所说的上下文腐化。它表现为重复、推理变慢、质量下降。所以通过大量评估，识别那个腐化阈值非常重要。它通常在 128k 到 200k，并用它作为上下文缩减的触发器。每当上下文大小接近它，你就必须触发上下文缩减。



---



**Speaker 2 18:09**



EN: But starting from compaction, not summarization\. And compaction doesn't mean like compressing the entire history\. You know, we might compact like the oldest 50% of tool calls while keeping the newer ones in full detail\. So the model still has fresh few\-shot examples of how to use tools properly\. Otherwise, like in the worst case, the model will imitate the behavior and output those compact formats with missing fields\. And that's totally wrong\.



ZH: 但要从压缩开始，而不是总结。压缩并不意味着压缩整个历史。你知道，我们可能压缩最旧的 50% 工具调用，同时保留较新的完整细节。这样模型仍有新鲜的 few\-shot 示例，知道如何正确使用工具。否则，在最坏情况下，模型会模仿那种行为，输出缺少字段的紧凑格式。那完全错误。



---



**Speaker 2 18:37**



EN: And after compaction, we have to check how much free context that we actually gain from this like compaction operation\. Sometimes like in this graph, after multiple rounds of compaction, the gain is tiny because like even if compacted, so use context\. And that's when we go for summarization\. But also keep in mind that when summarizing, we always use the full version of the data, not a compact one\. And we still like keep the last few tool calls, the tool results in full detail, not summary, because it can allow the model to know where left off and will continue like more smoothly\. Otherwise, you'll see like after summarization, sometimes the model will change its style, changes tone, and we find out like keeping a few like tool calls and tool result examples should really help\.



ZH: 压缩之后，我们必须检查从这次压缩操作中实际获得了多少空闲上下文。有时如图中所示，经过多轮压缩后，收益很小，因为即使压缩了，仍然占用上下文。这时我们就进行总结。但也要记住，总结时我们始终使用完整版本的数据，而不是紧凑版本。我们仍然保留最后几个工具调用、工具结果，保持完整细节，而不是摘要，因为这能让模型知道它停在哪里，从而更顺畅地继续。否则，你会看到总结后，有时模型会改变风格、改变语气。我们发现保留几个工具调用和工具结果示例确实有帮助。



---



**Speaker 2 19:22**



EN: Okay, now we've covered reduction and let's talk about isolation\. I really agree with Cognition's blog where they warn against using multi agent setups because like when you have multiple agents syncing information between them becomes nightmare\. But you know, this isn't a new problem\. Multi process or multi thread coordination has been a classic challenge in the early days of computer programming\. And I think we can borrow some wisdoms here\. I know how many like Go down coders here are here today\. But you know, in the Go programming language community, there's a famous quote from this gopher\. Do not communicate by sharing memory\. Instead, share memory by communicating\. Of course, this isn't directly about agents and it's sometimes even wrong for agents\. But I think the important thing is it highlights two distinct patterns here, which is by communicating or by sharing memory\. But if we translate the term memory here into context, we can see that parallel pretty clear\.



ZH: 好了，我们已经讲了缩减，现在谈隔离。我非常同意 Cognition 的博客，他们警告不要使用多智能体设置，因为当你有多个智能体时，它们之间同步信息会变成噩梦。但你知道，这不是新问题。多进程或多线程协调在计算机编程早期就是经典挑战。我认为我们可以借鉴一些智慧。我知道今天有多少 Go 开发者在这里。但在 Go 语言社区，有一句来自 Go gopher 的名言：“不要通过共享内存来通信。相反，通过通信来共享内存。”当然，这不直接关于智能体，有时甚至对智能体是错的。但我认为重要的是，它突出了两种不同模式：通过通信，或通过共享内存。如果我们把这里的“记忆”翻译成“上下文”，就能很清楚地看到这种对应。



---



**Speaker 2 20:24**



EN: By communicating is like the easier one to understand because it is the classic sub agent setup\. Here, for example, the main agent writes a prompt\. And if the prompt is sent to a sub agent, and the sub agent's entire context only consists of that instruction\. We think if a task has a like short clear instruction, and only the final output matters, say like searching a code base for a specific snippet\. Then just use the communication pattern and keep it simple because, you know, the main agent doesn't hear how the sub agent finds the code\. It only needs the result\. And this is what like Claude Code does, typically using its like task tool to delegate like separated clear task to some sub agents\.



ZH: “通过通信”是更容易理解的一种，因为它是经典的子智能体设置。例如，主智能体写一个提示，然后把提示发给子智能体，而子智能体的整个上下文只包含那条指令。我们认为，如果一个任务有简短清晰的指令，并且只有最终输出重要，比如在代码库中搜索某个特定片段，那就用通信模式并保持简单，因为主智能体不关心子智能体如何找到代码，它只需要结果。这就是 Claude Code 的做法，通常用它的 task 工具把分离的清晰任务委派给一些子智能体。



---



**Speaker 2 21:08**



EN: But for more complex scenarios, in contrast, by sharing memory means that the sub agent can see the entire previous context\. It means like all the tool use history, tool usage history, but the sub agent has its own system prompt and its own action space\.



ZH: 但对于更复杂的场景，相反，“通过共享记忆”意味着子智能体可以看到之前整个上下文。这意味着所有工具使用历史，但子智能体有自己的系统提示和自己的动作空间。



---



**Speaker 2 21:24**



EN: For example, like imagine a deep research scenario\. The final report depends on a lot of intermediate searches and notes\. And in that case, you should consider using the share memory pattern or in our language by sharing context\. Because like even you've, you can, yeah, you can save all that notes and searches into files and make the sub agent to read everything again\. But you're just wasting latency and context\. And if you count the amount of token, maybe you're using even more token to do this\. So we think like for those scenarios that require a full history, just use a share memory pattern\.



ZH: 例如，想象一个深度研究场景。最终报告依赖于大量中间搜索和笔记。在这种情况下，你应该考虑使用共享记忆模式，或者用我们的说法：通过共享上下文。因为即使你可以把所有这些笔记和搜索保存到文件中，让子智能体重新读一遍，你只是在浪费延迟和上下文。如果你计算 token 数量，也许这样做用了更多 token。所以我们认为，对于需要完整历史的场景，就使用共享记忆模式。



---



**Speaker 2 21:59**



EN: But be aware that like sharing context is kind of expensive because, you know, each sub agent has a larger input to prefill, which is like you'll spend more on like input tokens\. And since the system prompt and the action space differs, you cannot reuse the KV cache\. So you have to pay the full price\.



ZH: 但要注意，共享上下文有点昂贵，因为每个子智能体都有更大的输入需要预填充，这意味着你会在输入 token 上花更多钱。而且由于系统提示和动作空间不同，你无法复用 KV 缓存。所以你必须支付全价。



---



**Speaker 2 22:18**



EN: And finally, let's talk a little bit about like context offloading\. When people say offload, they usually mean like moving parts of the working context into external files\. As your system grows, especially if you decide to integrate MCP one day, you realize that the tools themselves also take up a lot of context and having too many tools in context leads to confusion\. We call it context confusion and the model might call like the wrong ones or even like non existing ones\. So we have to find a way to also offload the tools\.



ZH: 最后，我们稍微谈谈上下文卸载。当人们说卸载时，通常指把工作上下文的一部分移到外部文件。随着你的系统增长，尤其是如果你决定某天集成 MCP，你会意识到工具本身也占用大量上下文，上下文里工具太多会导致混乱。我们称之为上下文混淆，模型可能调用错误的工具，甚至不存在的工具。所以我们必须找到一种方法，也把工具卸载。



---



**Speaker 2 22:54**



EN: A common approach right now is like doing dynamic RAG on tool descriptions\. For example, like loading tools on demand based on the current task or the current status\. But that also causes two issues\. First of all, like, since tool definitions sit at the front of the context, yeah, your KV cache resets every time\. And most importantly, the model's past calls to removed tools are still in the context\. So it few\-shots the model into like calling invalid tools or invalid or using invalid parameters\. So to address this, we're experimenting with a new layered action space in Manus\.



ZH: 现在一个常见方法是对工具描述做动态 RAG。例如，根据当前任务或当前状态按需加载工具。但这也导致两个问题。首先，由于工具定义位于上下文前面，你的 KV 缓存每次都会重置。最重要的是，模型过去对已移除工具的调用仍然留在上下文中。于是它会 few\-shot 诱导模型调用无效工具，或使用无效参数。为了解决这个问题，我们在 Manus 中正在试验一种新的分层动作空间。



---



**Speaker 2 23:28**



EN: Well, essentially, we can let Manus choose from three different levels of abstractions\. No\. 1, function calling\. No\. 2, sandbox utilities\. And No\. 3, packages and API\. We'll go deeper into these three layers of layered action space\. Let's start from level 1, function calling\. And this is a classic\. Everyone knows it is schema safe thanks to constrained decoding\. But we all know the downside\. For example, we mentioned like breaking the cache and maybe too many tool calls will cause some confusion\. Too many tools may cause confusion\.



ZH: 本质上，我们可以让 Manus 从三个不同抽象层级中选择。第一，函数调用。第二，沙箱实用工具。第三，包和 API。我们会更深入讲这三层分层动作空间。从第一层开始，函数调用。这是经典做法。多亏约束解码，大家都知道它是 schema 安全的。但我们都知道缺点。例如，我们提到会破坏缓存，也许太多工具调用会导致混乱。工具太多可能导致混淆。



---



**Speaker 2 24:00**



EN: So in Manus, right now we only use a fixed number of atomic functions\. For example, like reading and writing files, executing shell commands, searching files in internet and maybe some like browser operations\. We think these atomic functions have super clear boundaries and they could work together to compose like much more complex workflows\.



ZH: 所以在 Manus，我们现在只使用固定数量的原子函数。例如读写文件、执行 shell 命令、搜索文件、互联网搜索，也许还有一些浏览器操作。我们认为这些原子函数边界非常清晰，它们可以一起组合成更复杂的工作流。



---



**Speaker 2 24:21**



EN: Then we offload everything else to the next layer, which is the sandbox utilities\. As you know, each Manus session runs inside a full virtual machine sandbox\. It's running on our own customized Linux system\. And that means Manus can use the shell commands to run pre\-installed utilities that we develop for Manus\.



ZH: 然后我们把其他所有东西卸载到下一层，也就是沙箱实用工具。如你所知，每个 Manus 会话都运行在一个完整的虚拟机沙箱中。它运行在我们自己定制的 Linux 系统上。这意味着 Manus 可以使用 shell 命令来运行我们为 Manus 开发的预装实用工具。



---



**Speaker 2 24:42**



EN: For example, we have some format converters, we have like speech recognition utilities and even a very special, we call it a Manus MCP CLI, which is how we call MCP\. We do not inject MCP tools into the function calling space\. Instead, we do everything inside that sandbox through the command line interface\. And utilities are great because like you can add new capabilities without touching the model's function calling space\. And you know, it's just some like commands pre\-installed in your computer\. And if you're familiar with Linux, you always know how to find those new commands\. And you can even run like dash dash help to figure out how to use a new tool\. And another good thing is for larger outputs, they can just write to files or return the result in pages\. And you can use all those Linux tools like grep, cat, less, more, like to process that results on the fly\. So the tradeoff here, like it's super good for large outputs, but it's also not that good for low latency back and forth interactions with the front end because like you always have to like, like to visualize the interactions of your agent and show it to the user\. So this is pretty tricky here, but we think like it already offloads a lot of things\.



ZH: 例如，我们有一些格式转换器、语音识别实用工具，甚至一个非常特殊的，我们叫它 Manus MCP CLI，这是我们调用 MCP 的方式。我们不把 MCP 工具注入函数调用空间。相反，我们在沙箱内部通过命令行界面做所有事情。实用工具很好，因为你可以添加新能力而不触碰模型的函数调用空间。而且你知道，它们只是预装在你电脑里的命令。如果你熟悉 Linux，你总是知道如何找到这些新命令。你甚至可以运行 `--help` 来弄清楚如何使用新工具。另一个好处是，对于较大的输出，它们可以写入文件或分页返回结果。你可以使用所有那些 Linux 工具，比如 grep、cat、less、more，来即时处理结果。所以这里的权衡是：它对大输出非常好，但对与前端低延迟的来回交互不太好，因为你总是需要可视化智能体的交互并展示给用户。所以这里相当棘手，但我们认为它已经卸载了很多东西。



---



**Speaker 2 25:58**



EN: And then we have another layer, the final layer, we call it packages and APIs\. You know, here, Manus can write Python scripts to call pre\-authorized API or custom packages\. For example, like Manus might use a 3D designing library for modeling or call a financial API to fetch market data\. And here, actually, like we've purchased all these API on behalf of our user and pay the money for them\. It's included in the subscription\. So we basically we have a lot of like API keys pre\-installed in Manus and Manus can access these APIs and the keys\. I think these are perfect for tasks that require lots of computation and memory, but do not need to push all that data into the model context\. For example, like imagine if you're analyzing a stock's entire year of price data, you don't feed the model all the numbers\. Instead, you should let the script compute it and only put the summary back into the context\. And, you know, since code and APIs are super composable, you can actually chain a lot of things in one step\. For example, like in a typical API, you can do like get city names, get city ID, get weather, all in one Python script\. There's also a paper like from one of my friend called CodeAct\. A lot of people were like discussing about it\. I think it's like the same idea because like code is composable and it can like, like, like do a lot of things in one step\. But also it's like, it's not schema safe\. It's very hard to do like a constrained decoding on code\.



ZH: 然后我们还有另一层，最后一层，我们叫它包和 API。这里，Manus 可以写 Python 脚本来调用预授权 API 或自定义包。例如，Manus 可能使用 3D 设计库建模，或调用金融 API 获取市场数据。实际上，我们已经代表用户购买并支付了所有这些 API，包含在订阅中。所以我们基本上在 Manus 中预装了很多 API key，Manus 可以访问这些 API 和 key。我认为这些非常适合需要大量计算和内存，但不需要把所有数据推入模型上下文的任务。例如，想象你分析一只股票一整年的价格数据，你不要把所有数字喂给模型。相反，你应该让脚本计算，只把摘要放回上下文。而且，由于代码和 API 高度可组合，你实际上可以在一步中串联很多事。例如，在典型 API 中，你可以获取城市名、获取城市 ID、获取天气，全在一个 Python 脚本里完成。还有一篇我朋友写的论文叫 CodeAct。很多人讨论它。我认为是同样的想法，因为代码可组合，可以在一步做很多事情。但它也不 schema 安全。对代码做约束解码非常难。



---



**Speaker 2 27:28**



EN: So we think you should find the right scenario for these features\. For us, we use all, as we mentioned, everything that's like that can handle inside a like compiler, interpreter, runtime\. We do that using code\. Otherwise we use like sandbox utilities or function calls\. And the good thing is if you have these three layers from model's point, all three levels still go through the standard function calls\. So the interface stays simple, cache friendly and orthogonal across functions because you know, we mentioned Sandbox Utilities\. You're still accessing these tools using the shell tool, accessing these tools using the shell function\. And also like if you're using APIs at third party applications, you're just using the file function to write or read file and then execute it in execute it using the shell function\.



ZH: 所以我们认为你应该为这些特性找到合适场景。对我们来说，我们全都用，如前所述，任何可以在编译器、解释器、运行时里处理的东西，我们用代码来做。否则我们使用沙箱实用工具或函数调用。好处是，如果你有这三层，从模型角度看，三层仍然都走标准函数调用。所以接口保持简单、缓存友好、功能正交，因为你知道，我们提到沙箱实用工具，你仍然使用 shell 工具访问这些工具，使用 shell 函数访问这些工具。同样，如果你使用第三方应用 API，你只是使用文件函数写或读文件，然后用 shell 函数执行它。



---



**Speaker 2 28:14**



EN: So we think it does not add like, like add overhead to the model\. It's still all the things that models are trained and they're already familiar with\. So let's zoom out and connect the five dimensions, offload, reduce, retrieve, isolate and cache\. You can find out that they're not independent\. We can see that offload and retrieve enables more efficient reduction and stable retrieve makes isolation safe\. But isolation, oh yeah, isolation also slows down context rot and reduces the frequency of reduction\. However, more isolation and reduction also affects cache efficiency and the quality of output\.



ZH: 所以我们认为它不会给模型增加开销。它仍然是模型训练过并已经熟悉的所有东西。让我们拉远一点，连接五个维度：卸载、缩减、检索、隔离和缓存。你会发现它们不是独立的。我们可以看到，卸载和检索使更高效的缩减成为可能，稳定的检索让隔离安全。但隔离，哦是的，隔离也会减慢上下文腐化，并降低缩减频率。然而，更多隔离和缩减也会影响缓存效率和输出质量。



---



**Speaker 2 28:54**



EN: So at the end of the day, I think context engineering is the science and art that requires a perfect balance between multiple potentially conflicting objectives\. It's really hard\. Alright, before we wrap up, I want to leave you with maybe one final thought\. And it's kind of the opposite of everything I just said, which is please avoid context over engineering\.



ZH: 所以归根结底，我认为上下文工程是一门科学和艺术，需要在多个可能冲突的目标之间取得完美平衡。这真的很难。好了，在我们结束之前，我想留给你们最后一个想法。它有点像我刚刚说的一切的反面：请避免上下文过度工程。



---



**Speaker 2 29:18**



EN: Like looking back at the had that like past six or seven months since Manus's launch, actually the big leaps we've ever seen didn't come from like adding more fancy context management layers or clever retrieval hacks\. They all came from simplifying or from like removing unnecessary tricks and trusting the model a little more\. Every time we simplified the architecture, the system got faster, more stable and smarter\. Because we think context engineering, the goal of context engineering is to make the model's job simpler but not harder\.



ZH: 回顾 Manus 发布以来过去六七个月，实际上我们见过的最大飞跃并不是来自添加更花哨的上下文管理层或聪明的检索技巧。它们都来自简化，或者移除不必要的技巧，并更信任模型一点。每次我们简化架构，系统都变得更快、更稳定、更聪明。因为我们认为上下文工程的目标，是让模型的工作更简单，而不是更难。



---



**Speaker 2 29:52**



EN: So if you like take one thing from today, I think it should be build less and understand more\. Well, thank you so much everyone\. And thanks again to Lance and the LangChain team for having me\. Can't wait to see what you guys all build next\. Now back to Lance\.



ZH: 所以如果今天你只带走一件事，我认为应该是：少构建，多理解。好了，非常感谢大家。再次感谢 Lance 和 LangChain 团队邀请我。迫不及待想看你们接下来构建什么。现在把时间交回给 Lance。



---



**Speaker 1 30:10**



EN: Yeah, amazing\. Thank you for that\. So we have a nice set of questions here, maybe we can start hitting them and we can kind of reference back to the slides if needed\. And Peak, are your slides available to everyone?



ZH: 是的，太棒了。谢谢你的分享。我们这里有一组不错的问题，也许我们可以开始回答，必要时可以回看幻灯片。Peak，你的幻灯片对大家都可用吗？



---



**Speaker 2 30:26**



EN: I can share the PDF version afterwards\.



ZH: 我稍后可以分享 PDF 版本。



---



**Speaker 1 30:28**



EN: Yes, sounds good\.



ZH: 好的，听起来不错。



---



**Speaker 1 30:38**



EN: Yeah, but I'm gonna start looking through some of the questions and maybe we can start with the more recent ones first\.



ZH: 好的，但我要开始看一些问题了，也许我们可以先从最近的问题开始。



---



**Speaker 1 30:49**



EN: So how does the LLM call the various shell tools? How does it know which tools exist and how to invoke them? Maybe explain a little bit about kind of the multi\-tier kind of sandbox you set up that you use with Manus\.



ZH: 那么，LLM 如何调用各种 shell 工具？它如何知道哪些工具存在以及如何调用它们？也许你可以稍微解释一下你们在 Manus 中设置的那种多层沙箱。



---



**Speaker 2 31:00**



EN: Yeah, I think like imagine you're the person that using a new computer, for example, if you know Linux, you can imagine like all the tools are located in /usr/bin\. So we actually we do two things\. First of all, we have a hint in the system prompt telling Manus that, hey, there's a lot of pre\-installed command line utilities located in a specific folder and also like for the most like frequently used ones, we already like injected in the system prompt, but it's super compact\. We do not like tell the agent how to use the tools\. We only list them and we can tell the agent that you can use the dash dash help flag safely because all the utilities are developed by our team and they have the same format\.



ZH: 是的，我想象一下你是一个使用新电脑的人。例如，如果你了解 Linux，你可以想象所有工具都位于 `/usr/bin`。所以我们实际上做两件事。首先，我们在系统提示中给 Manus 一个提示：嘿，有很多预装的命令行实用工具位于某个特定文件夹中。而且对于最常用的那些，我们已经注入到系统提示中，但非常紧凑。我们不会告诉智能体如何使用这些工具。我们只列出它们，并告诉智能体你可以安全使用 `--help` 标志，因为所有实用工具都由我们团队开发，格式相同。



---



**Speaker 1 31:41**



EN: Got it\. How about, I know you talked a lot about using file system\. What's your take on using indexing? And do you utilize like, do you spin up vector stores on the fly if the context you're working with gets sufficiently large, how do you approach that?



ZH: 明白了。我知道你谈了很多关于使用文件系统。你对使用索引有什么看法？如果处理的上下文变得足够大，你们会即时启动向量存储吗？你们怎么处理？



---



**Speaker 2 31:55**



EN: Yeah, I think like there's no right and wrong in this space\. Like you've mentioned\. But at Manus, we do not use index databases because right now, you know, every sandbox in Manus session is a new one and user want to like interact with things fast\. So actually we don't have the time to like build the index on the fly\. So we're more like Claude Code\. We rely on like grep and glob\. But I think like I, if you like consider to build something like more long term memory, or like if you want to integrate some like Enterprise Knowledge Base, you still have to rely on that like external index or vector index because like it's only about the amount of information that you can access\. But for like Manus, like it operates in a sandbox and for Coding Agent, you operate in the code base\. So it depends on the scale\.



ZH: 是的，我认为这个领域没有绝对对错，就像你提到的。但在 Manus，我们不使用索引数据库，因为现在每个 Manus 会话的沙箱都是新的，用户希望快速交互。所以我们实际上没时间即时构建索引。我们更像 Claude Code。我们依赖 grep 和 glob。但我认为，如果你考虑构建更长期记忆，或者集成某种企业知识库，你仍然必须依赖外部索引或向量索引，因为这关乎你能访问的信息量。但对于 Manus，它运行在沙箱中；对于编码智能体，它运行在代码库中。所以取决于规模。



---



**Speaker 1 32:43**



EN: Yeah, so that should, that's a good follow up then\. So let's say I'm a user, I have my Manus account, I interact with Manus across many sessions\. Do you have the notion of memory? So Claude has CLAUDE\.md files\. They persist across all the different sessions\. Claude Code\. How about you guys? How do you handle kind of long term memory?



ZH: 是的，那是个很好的后续问题。假设我是用户，我有 Manus 账户，我在很多会话中与 Manus 交互。你们有记忆的概念吗？Claude 有 CLAUDE\.md 文件，它们跨所有不同会话持久存在。Claude Code。你们呢？你们如何处理长期记忆？



---



**Speaker 2 32:59**



EN: Yeah, actually in Manus, we have a concept called knowledge, which is kind of like explicit memory\. For example, like every time you can tell Manus, hey, remember, like every time I ask for something, deliver it as an Excel, and it's not automatically inserted into some memory\. It will pop up a dialog and say, here's what I learned from our previous conversation and what you like accepted or rejected\. So this is the explicit one\. It requires user confirmation, but also like we're discovering new ways to do it more automatically\. For example, like pretty interesting thing in agents is that like compared to chatbots, user often like correct the agent more often\. For example, like a common mistake that Manus make is we're doing like data visualization\. You know, if we're using Chinese, Japanese or Korean, a lot of time there will be some font issues and there will be arrows in those rendered visualizations\. So the user will often say like, hey, you should like use Noto and CJK font\. And for these kind of things, the user will, the different user will have the same correction\. And we need to, we gonna find out a way to like to leverage these kind of collective feedback and use it\. That's kind of like we call it self\-improving agent or online learning, but in the parameter\-free way\.



ZH: 是的，实际上在 Manus 中，我们有一个叫 knowledge 的概念，有点像显式记忆。例如，每次你可以告诉 Manus：嘿，记住，每次我要东西时，用 Excel 交付。它不会自动插入某个记忆。它会弹出一个对话框说：这是我从我们之前对话中学到的，你接受还是拒绝。所以这是显式的，需要用户确认。但我们也正在发现更自动的方式。例如，智能体中一个有趣的事是，和聊天机器人相比，用户经常更频繁地纠正智能体。例如，Manus 常犯的一个错误是数据可视化。如果使用中文、日文或韩文，很多时候会有字体问题，渲染的可视化中会出现箭头。用户经常说：嘿，你应该使用 Noto 和 CJK 字体。对于这类事情，不同用户会有相同纠正。我们需要找到方法利用这种集体反馈。这就是我们所说的自改进智能体或在线学习，但是无参数方式。



---



**Speaker 1 34:13**



EN: Yeah, how about a different question that was raised here? And also I think about quite a bit, you mention towards the end of your talk that you gained a lot from removing things\. And a lot of that is probably because the fact that also the models are getting better, so model capabilities in increasing, and so you can kind of remove scaffolding over time\. How do you think about this? Cuz this is one of the biggest challenges that I face is like, over time, the model gets better and I can remove things like some parts of my scaffolding\. So you're building on top of this, the foundation that's like the water's rising and like, do you revisit your architecture every some number of months with new releases and just delete as the models get better\. And how do you approach that problem?



ZH: 是的，这里提出了另一个问题，我也经常思考。你在演讲最后提到，你从移除东西中获益很多。其中很大一部分可能是因为模型也在变好，模型能力在提升，所以你可以随着时间移除脚手架。你怎么看这个问题？因为我面临的最大挑战之一就是，随着时间推移，模型变好，我可以移除一些东西，比如我脚手架的一部分。你是在这个基础上构建，就像水涨船高。你们会每隔几个月随着新发布重新审视架构，并随着模型变好而删除吗？你如何处理这个问题？



---



**Speaker 2 34:55**



EN: Yeah, this is a super good question here because, you know, actually we have already refactored Manus for five times and we've launched Manus in March and now it's October already, five times\. So we think like you cannot stop because like models are not only improving but they're changing\. Model's behavior are change over time\. Like one way is you can work closely with those like model providers\. But we also have another like internal theory for how we evaluate or how we design our agent architecture, I covered a little bit on Twitter before\. It's basically like we all we do not care about is the, the, the, a static, the performance of a static benchmark\. Instead, we like we fix the agent architecture and we switch between models\.



ZH: 是的，这是个非常好的问题，因为实际上我们已经重构 Manus 五次了。我们在三月发布 Manus，现在已经是十月，已经五次了。所以我认为你不能停止，因为模型不仅在改进，还在变化。模型行为会随时间改变。一种方式是与模型提供商紧密合作。但我们也有另一个内部理论，关于如何评估或设计智能体架构，我之前在 Twitter 上稍微讲过。基本上，我们不关心静态基准上的静态性能。相反，我们固定智能体架构，然后在不同模型之间切换。



---



**Speaker 1 35:39**



EN: If the\.



ZH: 如果——



---



**Speaker 2 35:40**



EN: Like your architecture can gain a lot from switching from a weaker model to a stronger model, then somehow your architecture is more future proof because like the weaker models tomorrow might be as good as a stronger model today\. Yeah, so we think like switching like between weaker and strong models can give you some like early signals what will happen in next year and give you some time to prepare your architecture\.



ZH: 如果你的架构能从较弱模型切换到较强模型中获得很多收益，那么某种程度上你的架构更面向未来，因为明天的弱模型可能和今天的强模型一样好。是的，所以我们认为在弱模型和强模型之间切换，可以给你一些早期信号，告诉你明年会发生什么，并给你时间准备架构。



---



**Speaker 2 36:03**



EN: Yeah, so for Manus, like we often like do this kind of review like every one or two months and we often like to do some like, yeah, do some like, like research internally using like open source models and maybe like early access to prepare proprietary models to like prepare the next release, like even before the launch of the next model\.



ZH: 是的，所以对于 Manus，我们通常每一两个月做这种审查。我们经常在内部用开源模型做研究，也许还有提前访问专有模型，为下一次发布做准备，甚至在下一次模型发布之前。



---



**Speaker 1 36:27**



EN: Yeah, it's a good observation\. You can actually do testing of your architecture by toggling different models that exist today\. Yeah, that makes a lot of sense\. What about best practices, considerations for format for storing data? So like markdown files, plain text, log? What do you prefer in particular? I think obviously it's\. How do you think about that kind of file formats serve?



ZH: 是的，这是个好观察。你可以通过切换今天存在的不同模型来测试你的架构。是的，这很有道理。关于存储数据格式的最佳实践和考虑呢？比如 markdown 文件、纯文本、日志？你特别偏好什么？我认为显然……你怎么看这些文件格式的用途？



---



**Speaker 2 36:52**



EN: Yeah, I think like I, it's not about like plain text or markdown, but we always like prioritize line\-based formats because like it allows like the model to use like grep or like read from a range of lines and also like markdown can sometimes cause some troubles\. You know, models are trained to use markdown really well\. And sometimes it will, maybe for some model, I don't wanna say that name, but they often like\. I'll put too many bullet points if you use markdown too often\. Yeah, so actually we want to use more plain text\.



ZH: 是的，我认为关键不是纯文本还是 markdown，而是我们总是优先使用基于行的格式，因为它允许模型使用 grep，或从某个行范围读取。而且 markdown 有时会造成麻烦。你知道，模型被训练得很擅长使用 markdown。有时，也许对某些模型，我不想说名字，但如果你太频繁使用 markdown，它们经常会放太多项目符号。所以实际上我们想更多使用纯文本。



---



**Speaker 1 37:27**



EN: Yeah, make sense\. How about the topic of compaction versus summarization? Let's hit on summarization\. This is an interesting one that I've been asked a lot before\. How do you prompt to produce good summaries? So for example, summarization, like you said, it's irreversible\. So if you don't prompt it properly, you can actually lose information\. The best answer I came up with is just tuning your prompt for high recall\. But how do you approach this? So summarization, how do you think about prompting for summarization?



ZH: 是的，有道理。那关于压缩与总结的话题呢？我们谈谈总结。这是我以前被问过很多次的有趣问题。你如何提示以产生好的总结？例如，总结如你所说，是不可逆的。所以如果你提示不当，你实际上会丢失信息。我能想出的最好答案是：把提示调向高召回。但你怎么处理？对于总结，你如何思考提示？



---



**Speaker 2 37:57**



EN: Yeah, actually, like we tried a lot of, a lot like\. For optimizing the prompt for summarization\. But it turns out a simple approach works really well is that you do not use a free form like prompt to let the AI generate everything\. Instead, you could define it kind of a schema\. It's just a form\. There's a lot of fields and let the AI to fill them\. For example, like here are the files that I've modified and here's the goal of the user\. Here's what I left off\. And if you use this kind of like a more structured schema, at least like the output is kind of stable and you could iterate on this\. So just do not use like free form summarizations\.



ZH: 是的，实际上我们尝试了很多优化总结提示的方法。但事实证明，一个简单方法效果很好：不要使用自由形式的提示让 AI 生成一切。相反，你可以定义一个 schema。它就像一个表单，有很多字段，让 AI 填写。例如：这些是我修改过的文件，这是用户的目标，这是我停下的地方。如果你使用这种更结构化的 schema，至少输出会比较稳定，你可以迭代。所以不要使用自由形式的总结。



---



**Speaker 1 38:30**



EN: Got it\. Yeah, that's a great observation\. So you structured it up rather than free from summarization to enforce\. Yeah, certain things are always summarized\. Yeah, yeah, that makes a lot of sense\. How about with contacts? How about with compaction then? And actually, may I make sure I understood that? So with compaction, let's say it's a like a search tool, you have the raw search tool output\. And would it be that would be your raw message and then the compaction would just be like a file name or something\. Is that right?



ZH: 明白了。是的，这是个很好的观察。所以你把它结构化，而不是自由形式总结，以强制……是的，某些东西总是被总结。是的，这很有道理。那关于上下文？那压缩呢？实际上，我确认一下我理解得对不对。对于压缩，假设是一个搜索工具，你有原始搜索工具输出。那会是你的原始消息，然后压缩后就只是文件名之类的东西。对吗？



---



**Speaker 2 38:55**



EN: Yeah, it is\. It's not only about like the tool call, it's also like apply to the result of this tool\. Like, you know, we've, it's interesting, we find out that almost every action in Manus is this kind of like reversible\. If you can offload it to eh, to the file system or an external state\. And for both of these task, you already have a unique identifier for it\. For example, for file operations, of course, you have the file path\. For like browser operations, you have the URL\. And even for search actions, you have the query\. So it's, it's naturally it's already there\.



ZH: 是的，是这样。这不仅关于工具调用，也适用于这个工具的结果。你知道，有趣的是，我们发现 Manus 中几乎每个动作都是这种可逆的，只要你能把它卸载到文件系统或外部状态。而对于这两类任务，你已经有唯一标识符。例如，文件操作当然有文件路径。浏览器操作有 URL。甚至搜索动作，你有查询。所以它天然已经在那里了。



---



**Speaker 1 39:29**



EN: Okay\. This is a that's a great one\. And just wanna hit that again because it I've had this problem a lot\. So for example, I'm an agent that uses search\. I perform it, returns a token heavy tool call\. I don't return that whole tool message to the agent, I've done things like some kind of summarization, compaction and send the summary back\. But how do you approach that? Because you might want all that information to be accessible for the agent's next decision\. But you don't want that huge context block to live inside your message history\. So how do you approach that? You could send the whole message back, but then remove it later\. That's what Claude does now\. You could do a summarization first and send a summary over\. You could do, you could send everything and then do compaction so that later on you don't have the whole context in your message history\. You only have like a link to the file\. How do you think about that specifically? If you see what I'm saying?



ZH: 好的。这是个很好的问题。我想再强调一下，因为我经常遇到这个问题。例如，我是一个使用搜索的智能体。我执行搜索，返回一个 token 很重的工具调用。我不把整个工具消息返回给智能体。我做过某种总结、压缩，然后发回摘要。但你怎么处理？因为你可能希望所有这些信息都能被智能体下一步决策访问，但你不想让那个巨大的上下文块留在消息历史中。所以你怎么处理？你可以把整个消息发回，然后之后删除。这是 Claude 现在做的。你可以先做总结，发一个摘要过去。你可以发送所有内容，然后做压缩，这样之后消息历史里没有完整上下文，只有文件链接。你具体怎么看？你明白我的意思吗？



---



**Speaker 2 40:26**



EN: Yeah, I know\. Actually, it depends on the scenario\. For example, like for like complex search, I mean, for complex search, I mean, it's not just one query\. For example, like, yes, you have multiple queries and you want to like, like gather some important things and drop everything else\. In this case, I think we should use sub agents or internal, we call it agent as tool\. So for the from the model's perspective, it's still a kind of function maybe called advanced search\. It's a function called advanced search, but what it triggers is actually another sub agent\. But that sub agent is more like a workflow or agentic workflow that has a fixed output schema and that is the result that returns to the agent\. But for like other kinds of more simpler search, for example, just like searching Google, like we just use the full detail format and like append it into the context and rely on the compaction thing\. But also like we always like instruct the model to like write down like the intermediate insights or key findings into files in case that like the compaction that happens earlier, then the model expected\. And if you like do this really well, actually you don't lose a lot of information by compaction because sometimes like those like old tool calls are already irrelevant after sign\.



ZH: 是的，我知道。实际上这取决于场景。例如，对于复杂搜索，我指的是不止一个查询。比如，是的，你有多个查询，你想收集一些重要内容，丢掉其他所有东西。在这种情况下，我认为我们应该使用子智能体，或者内部我们叫它“智能体作为工具”。所以从模型角度看，它仍然是一种函数，也许叫 advanced search。它是一个叫 advanced search 的函数，但它触发的实际上是另一个子智能体。那个子智能体更像一个工作流或智能体工作流，有固定输出 schema，返回给智能体的就是那个结果。但对于其他更简单的搜索，比如只是搜 Google，我们就使用完整细节格式，追加到上下文中，然后依赖压缩。但我们也总是指示模型把中间洞见或关键发现写入文件，以防压缩比模型预期更早发生。如果你做得很好，实际上不会因为压缩丢失很多信息，因为有时那些旧工具调用在一段时间后已经无关了。



---



**Speaker 1 41:32**



EN: Yeah, that makes sense\. And I like the idea of agent as tool\. We do that quite a bit\. And it does make that, that is highly effective\. But that brings up another interesting point about, and you reference this a little bit, agent communication\. How do you address that? So Walden from Cognition has a very nice blog post talking about this is like a major problem that they have with Devin\. So like kind of communication between agents, how do you think about that problem? And yeah, ensuring sufficient information is transferred, but not overloading, like you said, the prefill of the sub agent with too much context\. So how do you think about that?



ZH: 是的，有道理。我喜欢“智能体作为工具”的想法。我们经常这么做，而且非常有效。但这引出了另一个有趣的点，你也稍微提到过：智能体通信。你怎么解决？Cognition 的 Walden 有一篇很好的博客，谈到这是他们在 Devin 上遇到的一个主要问题。也就是智能体之间的通信。你怎么看这个问题？以及如何确保足够的信息被传递，但又不会像你说的那样，用太多上下文压垮子智能体的预填充。你怎么看？



---



**Speaker 2 42:08**



EN: Yeah, you know, like, and then as we've like launch a feature called Wide Research a month ago\. Yeah, like it's basically like we call, yeah, internally we call it Agentic Map Reduce because we got inspired from the design of Map Reduce and it's kind of special for Manus because, you know, there's a full virtual machine behind the session\. So one way we pass like information or pass content from the main agent to sub agent is by sharing the same sandbox\. So the file system is there and you can only pass like the like different path here\. And I think like sending information to sub agent is not that hard\. The more complex thing is about how to like, like have the correct output from different agents\. And what we did here is like we have a trick for every time if the main agent want to spawn up a new sub agent or maybe 10 sub agent, you have to design, you have to let the main agent to define the output schema\. And in the subagent perspective, you have a special tool called Submit Result\. And we use constraint decoding to ensure that what the sub agent submits back to the main agent is the schema that is defined by the main agent\. Yeah, so you can imagine that this kind of map reduce operation, it will generate a kind of like spreadsheet\. And the spreadsheet is constrained by the schema\.



ZH: 是的，你知道，我们一个月前发布了一个叫 Wide Research 的功能。基本上，我们内部叫它 Agentic MapReduce，因为我们受 MapReduce 设计启发。它对 Manus 来说有点特殊，因为会话背后有一整台虚拟机。所以我们从主智能体向子智能体传递信息或内容的一种方式是共享同一个沙箱。文件系统在那里，你只需要传不同路径。我认为向子智能体发送信息并不难。更复杂的是如何从不同智能体获得正确输出。我们这里有一个技巧：每当主智能体想生成一个新子智能体，或者 10 个子智能体，你必须让主智能体定义输出 schema。从子智能体角度看，有一个特殊工具叫 Submit Result。我们使用约束解码，确保子智能体提交回主智能体的内容符合主智能体定义的 schema。是的，所以你可以想象这种 MapReduce 操作会生成一种类似电子表格的东西。电子表格受 schema 约束。



---



**Speaker 1 43:21**



EN: That's interesting thing that seems to come up a lot with you, how you design Manus\. You use schemas and structured outputs, both for summarization and for this agent communication\. So it's kind of like use schemas as contracts\. Yeah, between agent, sub agent or between like a tool and your agent to ensure that sufficient information is passed in a structured way, in a complete way, like we do summarization, you use a schema as well\.



ZH: 这很有意思，似乎在你设计 Manus 时经常出现。你使用 schema 和结构化输出，既用于总结，也用于这种智能体通信。所以这有点像把 schema 当作契约。是的，在智能体和子智能体之间，或者在工具和你的智能体之间，确保足够信息以结构化、完整的方式传递。就像总结时，你也使用 schema。



---



**Speaker 1 43:46**



EN: Okay, fantastic\. This is very helpful\. I'm poking around some other ancient questions here\. Any thoughts on models? Like I think you guys are use Anthropic, but do you work with open models? Do you do fine tuning? You talk a lot about kind of working with KV cache\. So for that, maybe using open models, how do you think about like model choice?



ZH: 好的，太棒了。这很有帮助。我再翻一些其他早先的问题。关于模型有什么想法？我想你们使用 Anthropic，但你们会用开源模型吗？你们做微调吗？你谈了很多关于 KV 缓存的工作。为了那个，也许使用开源模型。你怎么看模型选择？



---



**Speaker 2 44:08**



EN: Yeah, actually right now we don't use any like open source model right now because I think it's not about quality\. It's interestingly it's about cost\. You know, we often think that open source model can lower the cost, but if we're at the scale of Manus and if you're building a real agent, which the input is way longer than the output, then KV cache is super important\. And distributed KV cache is very hard to implement if you use like open source solutions and if you use like those like frontier LLM providers, they have more solid infrastructure for like distributed cache globally\. So sometimes like if you do the math, at least for Manus, we find out that using like these flagship models can sometimes can, they can be even more cheaper than like using open source models\.



ZH: 是的，实际上我们现在不使用任何开源模型，因为我认为问题不在质量。有趣的是，问题在成本。你知道，我们常认为开源模型能降低成本，但如果你处在 Manus 这样的规模，而且你在构建真正的智能体，输入远长于输出，那么 KV 缓存非常重要。如果你使用开源解决方案，分布式 KV 缓存非常难实现。而如果你使用那些前沿 LLM 提供商，他们拥有更稳固的全球分布式缓存基础设施。所以有时如果你算一下，至少对 Manus 来说，我们发现使用这些旗舰模型有时甚至比使用开源模型更便宜。



---



**Speaker 2 44:51**



EN: And right now we're not only using Anthropic first, like Anthropic model is the best choice for like agentic tasks, but we also like seeing like the progress in Gemini and in OpenAI's new model\. I think right now like these like Frontier Labs are not converging in directions\. For example, like if you're doing coding, of course you should use Claude\. And if you want to do like more multi modal, multi modality things you should use Gemini and OpenAI as well is super good at like complex math and reasoning\. So I think for application companies like us, one of our advantages that we do not have to build on top of only one model\. You can do some like task level routing or maybe even subtask or step level routing if you can like, like calculate, like if you can plurate that kind of KV cache validation\. So I think it's advantage for us and we do a lot of evaluations internally to know which models to use for which subtask\.



ZH: 现在，我们不仅优先使用 Anthropic——Anthropic 模型是智能体任务的最佳选择——但我们也看到 Gemini 和 OpenAI 新模型的进展。我认为现在这些前沿实验室并没有朝同一个方向收敛。例如，如果你做编码，当然应该用 Claude。如果你想做更多多模态的事情，应该用 Gemini。OpenAI 也非常擅长复杂数学和推理。所以我认为对于像我们这样的应用公司，优势之一是我们不必只构建在一个模型上。你可以做任务级路由，甚至子任务或步骤级路由，如果你能计算，如果你能利用那种 KV 缓存验证。所以我认为这是我们的优势，我们在内部做大量评估，了解哪个子任务该用哪个模型。



---



**Speaker 1 45:41**



EN: Yeah, yeah, that makes a lot of sense\. I wanna clarify one little thing\. So with KV cache, so what specific features from the, or yeah, what from the providers are using for cache management\. So, okay, I know like, and Anthropic has input caching as an example\. Yeah, that's what you mean\. Okay, got it\. Yeah, yeah, yeah, yeah, cool\. Okay, perfect\. Yeah, cool\. I'm just looking through some of the other questions\. Yeah, tool selection is a good one\. Right, so you're talking about this, you don't use like indexing of tool descriptions and fetching tools on the fly based on semantic similarity\. How do you handle that? Like what's the threshold for too many tools? Yeah, cool choices\. And classic\. How do you think about that?



ZH: 是的，这很有道理。我想澄清一点。关于 KV 缓存，你们具体使用提供商的哪些功能来做缓存管理？好的，我知道 Anthropic 有输入缓存，举个例子。是的，你就是这个意思。好的，明白了。酷。完美。我只是在看其他一些问题。工具选择是个好问题。对，你谈到你们不使用工具描述索引，也不基于语义相似度即时获取工具。你们怎么处理？工具太多的阈值是什么？是的，很酷的选择。经典问题。你怎么看？



---



**Speaker 2 46:34**



EN: Yeah, first of all, it depends on the model\. Different model has different capacity for like tools\. But I think in rule of thumb is try not to like include more than like 30 tools\. It's just a random number in my mind\. But actually, I think like if you're building a, we call it a general AI agent, like Manus, you want to make sure those like native functions are super atomic\. So actually, there are not that much like atomic function that we need to put inside the action space\. So like for Manus, we right now we only have like 10 or 20 like atomic function and everything else is in the sandbox\. Yeah, so we don't have to like to pull things like dynamic\.



ZH: 是的，首先，这取决于模型。不同模型对工具的能力不同。但我认为经验法则是尽量不要包含超过 30 个工具。这在我脑子里只是一个随机数字。但实际上，我认为如果你在构建一个我们称之为通用 AI 智能体的东西，比如 Manus，你要确保那些原生函数非常原子化。所以实际上，我们需要放进动作空间的原子函数并不多。所以对 Manus 来说，我们现在只有大约 10 到 20 个原子函数，其他一切都在沙箱里。是的，所以我们不必动态拉取东西。



---



**Speaker 1 47:14**



EN: Yeah, good point\. Actually, let's explain that a little bit more\. So you have, let's say, 10 tools that can be called directly by the agent\. But then, I guess it's like you said, the agent can also choose, for example, write a script and then execute a script\. Expanded action space hugely without giving it, like you don't have an independent tool for each possible script\. Of course, that's insane\. So a very general tool to like write a script and then run it does a lot\. Is that what you mean?



ZH: 是的，好观点。实际上，让我们再多解释一点。所以你有，比如说 10 个可以直接由智能体调用的工具。但然后，我想就像你说的，智能体也可以选择，比如写一个脚本，然后执行脚本。这极大地扩展了动作空间，而不必给它每个可能脚本都配一个独立工具。当然，那太疯狂了。所以一个非常通用的工具，比如写脚本然后运行它，就能做很多事。你是这个意思吗？



---



**Speaker 2 47:44**



EN: Yeah, exactly\. Because you know why we're super confident to call Manus a general agent? Because it runs on a computer and computers are Turing complete\. The computer is the best invention human\-like with us theoretically, like an agent can do anything that and maybe a junior intern can do using a computer\. So with the shell tool and the file and the text editor, we think it's already Turing complete\. So you can offload a lot of things to the sandbox\.



ZH: 是的，正是。因为你知道为什么我们非常有信心称 Manus 为通用智能体？因为它运行在计算机上，而计算机是图灵完备的。计算机是人类最好的发明之一，理论上，智能体可以做任何一个初级实习生用电脑能做的事情。所以有了 shell 工具、文件和文本编辑器，我们认为它已经是图灵完备的。所以你可以把很多东西卸载到沙箱。



---



**Speaker 1 48:09**



EN: Yeah, okay, that makes a lot of sense, right? And then how does Manus so, so is they're all so, okay, maybe I'll back up\. You mention CodeAct with Code Agents\. My understanding is the model will actually always produce a script and that'll then be run inside a code sandbox\. So every tool call is effectively like a script generated run\. It sounds like you do some hybrid where sometimes model can just call tools directly, but other times they can actually choose to do something in the sandbox\. Is that right? So it's kind of a hybrid approach\.



ZH: 是的，好的，这很有道理，对吧？然后 Manus 怎么……所以它们都是……好的，也许我退一步。你提到 CodeAct 和代码智能体。我的理解是，模型实际上总是产生一个脚本，然后在代码沙箱中运行。所以每次工具调用实际上都是生成脚本并运行。听起来你们做的是某种混合方式：有时模型可以直接调用工具，但其他时候它们可以选择在沙箱里做某事。对吗？所以这是一种混合方法。



---



**Speaker 2 48:46**



EN: Yeah, I think this is super important because like, actually we try to use entirely to use CodeAct for Manus\. But the problem is if you're using code, you cannot leverage like constrained decoding and things can go\.



ZH: 是的，我认为这非常重要，因为实际上我们尝试过让 Manus 完全使用 CodeAct。但问题是，如果你使用代码，你无法利用约束解码，事情可能出错。



---



**Speaker 1 48:57**



EN: Wrong\.



ZH: 出错。



---



**Speaker 2 48:58**



EN: Yeah, but you know, CodeAct has some like special use cases as I mentioned earlier in slides\. For example, like processing a large amount of data\. You don't have to like pour everything in the tool result, but you put it inside like maybe the runtime memory of Python and you only get the result back to the model\. So we think you should do it like in a hybrid way\.



ZH: 是的，但你知道，CodeAct 有一些特殊用例，正如我之前在幻灯片中提到的。例如处理大量数据。你不必把所有东西都倒进工具结果，而是放进 Python 运行时内存，只把结果返回给模型。所以我们认为应该以混合方式来做。



---



**Speaker 1 49:18**



EN: Got it\. Allow for tool calling and use some number of tools, maybe 10 or something that is called directly, some number of tools that actually run in the sandbox itself\. Yes, perfect\. That makes a ton of sense\. Very interesting\.



ZH: 明白了。允许工具调用，并使用一些工具，也许 10 个左右直接调用，还有一些工具实际上在沙箱本身中运行。是的，完美。这非常合理。非常有趣。



---



**Speaker 1 49:34**



EN: And then maybe how do you keep a reference of all the previously gen, I guess, you have? So you basically generate a bunch of files\. Oh, actually, sorry, maybe I'll talk about something else\. How about planning? Tell me about planning\. And I know Manus has this to\-do tool or a general to\-do list and start tasks\. Yeah, tell me about that\.



ZH: 然后，也许你如何保留所有之前生成的引用，我猜你有？所以你基本上生成一堆文件。哦，实际上，抱歉，也许我谈点别的。规划呢？告诉我规划。我知道 Manus 有这个 to\-do 工具或通用 to\-do 列表并开始任务。是的，告诉我。



---



**Speaker 2 49:52**



EN: Yeah, I think this is very interesting because like at the beginning, Manus uses that to\-do\.md paradigm\. Like it's kind of\. Yeah, I don't want to use it\. Where's stupid actually, it wastes a lot of turns, you know, like back in maybe March or April, like if you like check that the log of some Manus task, maybe like 1/3 of the action is like updating the to\-do list and waste a lot of like, like tokens\. Yeah, so right now we using a more like structured planning\. For example, like if you use Manus, there's a planner at the bottom of like the system internally\. It's also kind of a tool called it's we implemented using the agent as tool paradigm so that like there's a separate agent that is managing the plan\. So actually right now, the latest version is we're no longer using that to\-do\.md thing\. Of course, like to\-do\.md still works and it can generate like good results\. But if you wanna save tokens, you can find another way\.



ZH: 是的，我认为这很有趣，因为一开始 Manus 使用 `todo.md` 范式。有点像……是的，我不想用它。实际上它很蠢，浪费很多轮次。你知道，大概三月或四月，如果你查看某些 Manus 任务的日志，可能三分之一的操作是在更新 to\-do 列表，浪费大量 token。所以现在我们使用更结构化的规划。例如，如果你使用 Manus，系统内部底部有一个 planner。它也是一种工具，我们用“智能体作为工具”范式实现，所以有一个单独的智能体管理计划。所以实际上，最新版本我们不再使用 `todo.md` 那套。当然，`todo.md` 仍然有效，也能产生好结果。但如果你想节省 token，可以找别的方式。



---



**Speaker 1 50:45**



EN: Got it\. See, you like a planner agent and it's more like for a subtask will be more like agent as tool call type things\.



ZH: 明白了。所以你偏好规划器智能体，而且对于子任务，更像是“智能体作为工具调用”那种东西。



---



**Speaker 2 50:52**



EN: Yeah, got it\. And you know, it's very important to have a separate agent to that has a different perspective\. So it can do some like external reviews and you can use different models for planning\. For example, like, oh yeah, sometimes like Grok can generate some very interesting insights\.



ZH: 是的，明白了。而且你知道，拥有一个具有不同视角的独立智能体非常重要。所以它可以做某种外部审查，你可以为规划使用不同模型。例如，哦是的，有时 Grok 可以生成一些非常有趣的洞见。



---



**Speaker 1 51:07**



EN: Yeah, well, that's a great one actually\. So think about like multi agent then\. And so like, how do you think about that? So you might have like a planning agent with its own context window, makes a plan, reduces like some kind of plan object\. Maybe it's a file or maybe it just calls sub agents directly\. How do you think about that? Like, yeah, how many different sub agents do you typically recommend using?



ZH: 是的，这其实是个很好的点。那么想想多智能体。你怎么看？所以你可能有一个规划器智能体，有自己的上下文窗口，制定计划，缩减成某种计划对象。也许是一个文件，或者它直接调用子智能体。你怎么看？通常推荐使用多少不同的子智能体？



---



**Speaker 2 51:29**



EN: Yeah, I think this is also like, depends on your design, but here at Manus, actually Manus is not kind of like the typical multi agent system\. For example, like we've seen a lot of like different agent that divides by role\. For example, like you have a designer agent or design or like programming agent, manager agent\. We don't do that because we think like why we have this is because this is how like human company works\. And this is due to the limitation of like human context\. So in Manus, yeah, Manus is a multi\-agent system, but we do not divide by role\. We only have very few agents\. For example, we have a huge like general executor agent and a planner agent and a knowledge management agent and maybe like some, yeah, data API registration agent\. Yeah, so we are very cautious about adding more sub agents because of the reason that we've mentioned before\. Communication is very hard and we implement more kinds of like sub agents as agents tools as we mention before\.



ZH: 是的，我认为这也取决于你的设计。但在 Manus，实际上 Manus 不是那种典型的多智能体系统。例如，我们见过很多按角色划分的不同智能体。比如你有设计师智能体，或编程智能体、管理器智能体。我们不这么做，因为我们认为这种做法来自人类公司运作方式，是由于人类上下文的限制。所以在 Manus，是的，Manus 是一个多智能体系统，但我们不按角色划分。我们只有非常少的智能体。例如，我们有一个巨大的通用执行器智能体、一个规划器智能体、一个知识管理智能体，也许还有一些数据 API 注册智能体。是的，所以我们对添加更多子智能体非常谨慎，因为之前提到的原因。通信非常难。我们更多把子智能体实现为“智能体工具”，如之前提到的。



---



**Speaker 1 52:21**



EN: Well, yeah, that's a group\. Yeah, that's a great point\. I see this mistake a lot, or I don't know if it's a mistake, but you see anthropomorphizing agents a lot\. Like it's my designer agent and I think it's kind of a forced analogy to think about like a human org chart in your sub agent\. So got it\. So for you, it's like a planner and knowledge manager might do what? Like that will be the task with knowledge manager, like\.



ZH: 嗯，是的，那是一组。是的，这是个很好的点。我经常看到这个错误，或者我不知道算不算错误，但你经常看到人们把智能体拟人化。比如“这是我的设计师智能体”，我认为把子智能体想成人类组织架构图有点牵强。所以明白了。对你来说，就是规划器和知识管理器，知识管理器会做什么？比如知识管理器的任务是什么？



---



**Speaker 2 52:47**



EN: Yeah, I, it's even like more simple, as you mention, like we have a knowledge system and Manus what the knowledge agent does is that it reviews like the conversation between the user and the agent and figure out like what should be like saved in the long term memory\. So it's that simple\.



ZH: 是的，实际上更简单，如你所说，我们有一个知识系统，Manus 中知识智能体做的是审查用户和智能体之间的对话，弄清楚什么应该保存到长期记忆中。就这么简单。



---



**Speaker 1 53:02**



EN: Got it\. Okay, guys\. Like it's like a, yeah, memory manager planner and then you have sub agents that can just take on like a general executor sub agent that could just call all the tools or actions in the sandbox\. That makes sense\. Keep it simple\. I like that a lot, right? That makes a lot of sense\. Yeah, let me see if there's any, there's a bunch of questions here, but we did hit a lot\. So that's actually, how about guardrailing someone ask a question about kind of safety and guardrailing\. How do you think about this? I guess that's a nice thing about a sandbox, but tell me a little bit about that, how you think about it\.



ZH: 明白了。好的，各位。就像是一个记忆管理器、规划器，然后你有子智能体，比如通用执行器子智能体，可以调用沙箱中的所有工具或动作。这有道理。保持简单。我很喜欢。是的，很有道理。让我看看还有没有……这里有很多问题，但我们已经回答了很多。那么，关于护栏，有人问安全和护栏。你怎么看？我想沙箱的一个好处就是这个，但请稍微讲讲你怎么看。



---



**Speaker 2 53:39**



EN: Yeah, I think this is very sensitive question because like, you know, if you have a sandbox that's connected to the internet, everything is dangerous\. So yeah\.



ZH: 是的，我认为这是一个非常敏感的问题，因为你知道，如果你有一个连接互联网的沙箱，一切都很危险。所以是的。



---



**Speaker 1 53:49**



EN: We have put a lot\.



ZH: 我们投入了很多——



---



**Speaker 2 53:50**



EN: Of effort like in guardrailing, like at least we do not let the information to get out of the sandbox\. For example, like if you like got prompt injected, we have some like checks on like outgoing traffic, for example, like we'll ensure that no like token things will go out of the sandbox\. And if the user wants to like print something out of sandbox, we have those kind of like what we call it, removing, yeah, removing things and to ensure that no information go out to the sandbox\.



ZH: 我们投入了很多精力做护栏，至少我们不让信息离开沙箱。例如，如果你被提示注入，我们会对出站流量做一些检查。例如，确保没有 token 之类的东西离开沙箱。如果用户想把某些东西打印出沙箱，我们有那种我们叫它……移除，是的，移除机制，确保没有信息离开沙箱。



---



**Speaker 2 54:17**



EN: But you know, for another kind of thing is that we have a browser inside of Manus and the browser is very complicated\. For example, like if you log into some like your websites, you can choose to let Manus to persist your login state\. And this turns out to be like, like very tricky because like sometimes the content of the web page can also be like malicious\. Maybe they could, they're doing like prompt injection\. And this I think is somehow like out the scope for an application company\. So we're moving, we're working very closely with those computer use a model provider, for example, like partnering with Google\. Yeah, they're adding a lot of guardrails here\.



ZH: 但你知道，另一件事是我们在 Manus 内部有一个浏览器，浏览器非常复杂。例如，如果你登录某些网站，你可以选择让 Manus 保持你的登录状态。这结果非常棘手，因为有时网页内容也可能是恶意的。也许它们在做提示注入。我认为这某种程度上超出了应用公司的范围。所以我们正在与那些 computer use 模型提供商非常紧密地合作，例如与 Google 合作。是的，他们在这里加入了很多护栏。



---



**Speaker 2 54:51**



EN: So right now in Manus, every time you do some like sensitive operations, whether or inside the browser or in the sandbox, Manus will require a manual confirmation and you must accept it or otherwise you have to take over it to finish it yourself\. So I think like it's pretty hard for us to like design a like kind of a very like well designed solution, but it's a progressive approach\. So right now we're letting the user to take over more frequently\. But like if the guardrail itself in the model gets better, we can do less\.



ZH: 所以现在在 Manus，每次你做某些敏感操作，无论是在浏览器内还是在沙箱内，Manus 都会要求手动确认。你必须接受，否则你必须接管并自己完成。所以我认为我们很难设计一个非常完善的解决方案，但这是一个渐进式方法。现在我们会让用户更频繁地接管。但如果模型自身的护栏变好，我们可以少做一点。



---



**Speaker 1 55:22**



EN: Probably the topic of emails, this has been discussed a lot, quite a bit online\. If you probably seen, you know, Claude Code, they talked a lot about just doing less formal evals, at least for code\. Cuz code evals are more or less saturated\. Lots of internal dogfooding\. Anything about evals? Are they useful? What evals are actually useful? Yeah, it's your approach\.



ZH: 大概还有评测的话题，这在网上被讨论了很多。你可能见过，Claude Code 他们谈了很多关于少做正式评测，至少对代码来说。因为代码评测或多或少已经饱和。很多内部 dogfooding。关于评测有什么想法？它们有用吗？什么评测真正有用？是的，你的方法是什么？



---



**Speaker 2 55:41**



EN: You know, at the beginning, at the launch of Manus, we're using like public academic benchmarks like GAIA\. But then like after we're after launching to the public, we find out that it's super misaligned\. You know, models are, yeah, that gets like high scores of GAIA, the user don't like it\. So right now, yeah, we use like 3 to, we have three different kinds of evaluations\. First of all, most importantly is that for every like completed session in Manus, we'll request the user to like give a feedback, you give 1 to 5 stars\. Yeah, this is the old standard\. Like we always care about like the average user rating\. This is No\. 1\. And No\. 2, we're still using some like, like internal automated test with like verifiable results\. For example, like we have like created our own data set with like clear answers\. But also like we, we, we, yeah, we still use a lot of like public academic benchmarks, but we also created some datasets that's more focus on execution because like most benchmark out there are more about like read only test\. So we design some like executing test or transactional task because we have the sandbox, we can like frequently reset the test environment\. So these are the automated parts\. And most importantly, number No\. 3, we have a lot of interns\. You know, you have to use a lot of real human interns to do like, like evaluations on things like, yeah, website generation or data visualization because like, it's very hard to design a good reward model that knows whether the output is visually appealing\. Like it's about the taste\. So we still rely on a lot about a lot\.



ZH: 你知道，一开始，Manus 发布时，我们使用 GAIA 这样的公共学术基准。但后来向公众发布后，我们发现它非常不对齐。你知道，模型……是的，在 GAIA 上得高分，但用户不喜欢。所以现在，我们使用三种不同评测。首先，最重要的是，对于 Manus 中每个完成的会话，我们会请求用户反馈，给 1 到 5 星。是的，这是老标准。我们始终关心平均用户评分。这是第一。第二，我们仍然使用一些内部自动化测试，带有可验证结果。例如，我们创建了自己的数据集，有清晰答案。但我们也仍然使用很多公共学术基准。不过我们还创建了一些更关注执行的数据集，因为外面大多数基准更多是只读测试。所以我们设计了一些执行测试或事务性任务，因为有了沙箱，我们可以频繁重置测试环境。这些是自动化部分。最重要的是第三，我们有很多实习生。你知道，你必须用很多真实人类实习生来做评测，比如网站生成或数据可视化，因为很难设计一个好的奖励模型来判断输出是否视觉上吸引人。这关乎品味。所以我们仍然大量依赖……大量依赖。



---



**Speaker 1 57:07**



EN: Yeah, yeah, yeah, perfect\. Let me ask you\. I know you're we're coming up on time, but I do wanna ask you about this emerging trend of reinforcement learning with verifiable rewards versus just building tool calling agents\. So like Claude Code, extremely good and they have the benefit because they built the harness and they can perform RL on their harness and it can get really good with the tools they provide in the harness\. Do you guys do RL or how do you think about that? Cuz of course, in that case, you would have to give you some open models\. I've been playing with this quite a bit lately\. I think about that just like using tool calling with models providers versus doing all RL yourself inside your environment with your harness\. How do you think about that?



ZH: 是的，是的，完美。让我问你。我知道我们时间快到了，但我确实想问你这个新兴趋势：带可验证奖励的强化学习，与只构建工具调用智能体之间的对比。比如 Claude Code 极其优秀，他们有一个优势，因为他们构建了 harness，可以在自己的 harness 上做 RL，并且在 harness 中提供的工具上变得非常强。你们做 RL 吗？你怎么看？因为当然，那样的话，你必须在某些开源模型上做。我最近玩了不少。我思考的是：只使用模型提供商做工具调用，还是在你自己的环境中用你的 harness 自己做所有 RL。你怎么看？



---



**Speaker 2 57:51**



EN: Yeah, I mention like before starting Manus, I was kind of model training guy\. I've been doing like pre\-training\. Yeah, training RL for a lot of years\. I have to say that right now, if you like, if you have like in the in like sufficient resource, you could try\. But actually like we, as I mentioned earlier, MCP is a big changer here because like if you want to support MCP, you're not using a fixed action space\. And if it's not a fixed action space, it's very hard to design a good like reward and you cannot generate a lot of like the rollout and feedbacks will be unbalanced\. So if you want to build a model using like that supports MCP, you're literally building a foundation model by yourself\. So I think like, yeah, everyone in the in the community, like model companies, they're doing the same thing for you\.



ZH: 是的，我之前提到，在创办 Manus 之前，我算是做模型训练的人。我做过预训练。是的，做 RL 训练很多年。我必须说，现在如果你有足够资源，可以尝试。但实际上，如我之前提到的，MCP 是一个很大的改变者，因为如果你想支持 MCP，你就不在使用固定动作空间。如果不是固定动作空间，就很难设计好的奖励，你也无法生成大量 rollout，反馈会不平衡。所以如果你想构建一个支持 MCP 的模型，你实际上是在自己构建基础模型。所以我认为，社区里的每个人，模型公司，他们都在为你做同样的事。



---



**Speaker 2 58:34**



EN: So right now, I don't think we should spend that much time on doing RL right now\. But like, as I mentioned earlier, we're just discovering, like, like exploring new ways to do like\. We maybe we call it like personalization or some sort of online learning, but using like parameter free way, for example, like collective feedbacks\.



ZH: 所以现在，我认为我们不应该在 RL 上花那么多时间。但如我之前提到的，我们正在发现、探索新方式。也许我们称之为个性化或某种在线学习，但使用无参数方式，例如集体反馈。



---



**Speaker 1 58:53**



EN: One little one along those lines is I is it the case that for example, and topics done reinforcement learning and verify rewards on some set of tools using Claude Code, have you found that you can kind of mock yours, yours, yours, your harness to use similar tool names to kind of unlock the same capability, if that makes sense\. Like for example, like I believe they've just, you know, they've obviously performed, you know, they u and utilize globally uses grep uses some other set of tools for manipulating the file system\. Can you effectively reproduce that same functionality I in having the exact same tools with same tool name, same descriptions in your harness or kind of how do you think about that? Like unlocking the\. Yeah, right\. You see, I'm saying, yeah\.



ZH: 沿着这些思路，有一个小问题：比如说，Anthropic 用 Claude Code 在某些工具集上做了强化学习和可验证奖励。你们是否发现，可以模仿你们的 harness，使用相似的工具名，来解锁同样的能力？如果这说得通的话。例如，我相信他们显然做了……你知道，他们全局使用 grep，使用一些其他工具来操作文件系统。你能有效地复现同样功能吗？在你的 harness 中使用完全相同的工具、相同工具名、相同描述？或者你怎么看？比如解锁……是的，对。你明白我的意思，是的。



---



**Speaker 2 59:37**



EN: Yeah, I know the clear answer here\. But for us, we actually try not to use the same name because like, it will, like if you design your own function, you maybe have like different requirements for that function and the parameters at the input arguments might be different\. So you don't want to like confuse the model\. Like if the model is trained on a lot of like post training data that yeah, have some like internal tools\. You don't want to let the model to be confused\.



ZH: 是的，我知道这里没有明确答案。但对我们来说，我们实际上尽量不使用相同名字，因为如果你设计自己的函数，你可能对该函数有不同要求，输入参数也可能不同。所以你不想让模型困惑。如果模型在大量后训练数据上训练过，是的，有一些内部工具。你不想让模型困惑。



---



**Speaker 1 01:00:01**



EN: Okay, okay, got it\. Perfect\. Well, I think we're actually at time and I wanna respect your time cuz I know it's early\. You're in Singapore\. It's very early for you\. So, well, this was really good\. Thank you\. We'll definitely make sure this recording is\.



ZH: 好的，好的，明白了。完美。嗯，我想我们实际上到时间了，我想尊重你的时间，因为我知道现在很早。你在新加坡，对你来说非常早。所以，这次真的很好。谢谢。我们一定会确保这段录像——



---



**Speaker 1 01:00:19**



EN: Available\. We'll make sure slides are available\. Any parting things you wanna mention, things you wanna call out? Calls to action? Yeah, people should go use Manus, but the floor is yours\.



ZH: 可用。我们会确保幻灯片可用。你有什么临别的话想说，或者想特别提一下？行动号召？是的，人们应该去用 Manus，但舞台交给你。



---



**Speaker 2 01:00:33**



EN: Yeah, I just wanna say everybody try this\. We have a free\.



ZH: 是的，我只想说大家都来试试。我们有免费——



---



**Speaker 1 01:00:36**



EN: Tier\. Yeah, yeah, yeah, absolutely\. Hey, thanks a lot, Peak\. I love to do this again sometimes\.



ZH: 套餐。是的，是的，当然。嘿，非常感谢，Peak。希望以后有机会再做一次。



---



**Speaker 2 01:00:43**



EN: Yeah, thanks for having me\. Okay, bye bye\.



ZH: 是的，谢谢邀请我。好的，拜拜。



---



以上为完整中英文对照整理版。

