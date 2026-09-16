# tiny-inference

You type something into ChatGPT. A few seconds later, an answer appears.

Seems like magic. It is not. Boring world, I know.

Under the hood there is a ridiculous amount of stuff happening. Tokenization, embeddings, Transformer layers, KV cache, batching, scheduling, memory management, GPUs grinding through matrix math. And that is just for one request.

Now serve that for hundreds or thousands of users at once without running out of VRAM or burning your cloud budget.

That is what this project is about.

## Goal

Learn how LLM inference infra really works by building it. By hand. One layer at a time. 

Where we are headed looks roughly like this:

```
Internet -> Cloudflare -> LiteLLM -> llm-d -> vLLM pool -> LMCache / KV transfer -> GPUs
                                      + Prometheus + Grafana watching all of it
```

The model is not the point. The infrastructure is.

Not "I deployed an LLM." More like "I know why it works, where it chokes, what happens when it dies, and what it costs."

## How to follow along

This is a follow along lab. The blog posts walk through the thinking, the `docs/` folder mirrors those posts (TBA as they get published), and this repo holds the stuff you can actually run: scripts, configs, manifests, benchmark profiles, raw results.

Read the post to get the why. Come here to get the how. Run the same steps, compare your numbers to mine, and break things yourself. That is the whole idea.

The loop for everything: learn a bit, read the docs, build it by hand, measure, break it, debug it, understand why, write it down, automate, then add exactly one new thing.

## What we will use

Nothing exotic at the start. The stack grows as the lab grows:

* Civo GPU VMs, starting with one A100 40GB. Raw VMs, no managed Kubernetes.
* Linux, NVIDIA drivers, CUDA, container runtime. The boring stuff that has to work first.
* vLLM as the inference engine. One model, pinned for the whole lab so benchmarks stay comparable.
* LiteLLM in front of vLLM for auth, routing, budgets, logging.
* k3s built by hand when we get to Kubernetes. No managed control plane.
* llm-d for inference aware routing once generic load balancing starts to hurt.
* LMCache plus KV transfer stuff (NIXL etc) when we have proven the KV problem is real.
* NCCL and multi GPU networking only when one GPU is no longer enough.
* Prometheus and Grafana for observability from the middle onward.
* GuideLLM for benchmarking. TTFT, TPOT, latency, throughput, concurrency sweeps. Raw JSON kept so you can check my math. Other small tools and scripts as needed, nothing hidden.

No Bedrock, no SageMaker, no managed serving. That would skip the learning.

## Blog posts

* Warmup (not part of the series): [How an LLM Inference Engine Works](https://medium.com/@0xA1M/how-an-llm-inference-engine-works-df2b9a921d12). What happens between Enter and tokens. Tokenization, Transformer, prefill and decode, KV cache, TTFT and TPOT, batching, scheduler, PagedAttention. Read that first if you have not.
* tiny-inference series: TBA. Each post will have real numbers, real failures, real root causes. Not theory. `docs/` will mirror each post as it lands.

## License

MIT. See [LICENSE](./LICENSE).
