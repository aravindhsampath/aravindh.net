---
title: "Queueing theory for performance engineers"
date: 2020-02-01T13:53:47+01:00
draft: true
featured_image: "/images/queue_cover.png"
description: "Queueing theory and fundamental statistics for thinking about computer systems' capacity and performance. "
toc: true
tags: ["Queueing theory", "performance", "statistics"]
---

## Queueing theory - What?

 - Queues are everywhere - from grocery stores to computer systems to Kanban boards to public restrooms to getting your new iPhone.

 - To a computer engineer queues may be analogous to "buffers".

 - Why have queues? 
 	- Increase availability/utilisation of a server, by absorbing variability in arrival times of requests and having them ready to be serviced when the server is ready to accept more work. 
 	- Add resilience by introducing an opportunity for asynchronism.

- If queues are so good, why not have them everywhere?, what is the downside?
	- Queues add latency by virtue of requests spending time "waiting" in addition to "executing/getting serviced".
	- The increase of wait time in relation to load/utilisation of the system is __nonlinear__. 50% additional load to an existing system does not result in 50% increase in wait times and everything else - this is counter-intuitive to most people.

In the real world, what makes you think about reaching out to queueing theory? Answering questions like

* How long are we spending waiting/queued up?
* If I increase the load to this system, what would it do to my wait times? 
* How much capacity is needed to meet demand at acceptable response times?
* How much performance/capacity headroom do we have before we rush to add more servers?
* Should I buy fewer faster CPUs or more slower CPUs?
* What is the maximum rate of work I can achieve given how much time it takes in average to do the work.

A key tip in the world of queueing theory :

- Data may be simplified by the use of "averages" to ignore the variability just to see the big picture.



## Terminology

**Arrival rate** - λ - How often new requests/customers arrive at the system. It is no. of customers/requests per time.

**Service time** - S - How long it takes to service a request/customer in average.

**Service rate** - μ - inverse of S. 1/S

**wait time** - W - How long a request/customer waits in queue in average. 

**Response time / Residence time** - Total time spent in system (wait + service) in average.

**Throughput** - X - Rate at which work is completed. 

**Queue length** - Q - Average number of requests/customers waiting in queue + those that are being serviced.

**Utilisation** - ρ or U - Fraction of time the server(s) are busy actively servicing requests/customers.


## Simple queue

M/M/1 queue - [Kendall notation](https://en.wikipedia.org/wiki/Kendall%27s_notation)

M - Markov or memoryless - arrivals occurs according to Poisson process.

M - Markov or memoryless - service times are according to Poisson process.

1 - Single server.


<img class="pt3" src="/images/simple_queue.png" class="w-40 f5" alt="Simple M/M/1 queue"></img>

Requests arrive at a rate λ (λ=A/T), and wait for time W, and get serviced for time S(μ=1/S).

If average arrival rate, λ is the same or less than service rate, μ, then the system is in equilibrium or steady state. If λ > μ, queues grow indefinitely. None of the below formulae applies at that state.

λ = A / T  (Arrivals per time period)

X = C / T   (Completions per time period)

S = B / C    (Busy time per completion)

U = B / T    (Percentage of time spent busy)

R = W + S    (Response time is wait time + service time)

Pro Tip: 

In real life systems, measuring response time with just one request to an M/M/1 system gives you the service time(S) since wait time is zero.

## Little's law

Q = λ * R = X * R

[Queue length] = [Arrival rate] * [Response time] = [Throughput] * [Response time]

U = λ * S

[Utilisation] = [Arrival rate] * [Service time]

R = S / (1 - U) 

[Response time] = [Service time] / (1 - [Utilisation])

Example:
A microservice that is responsible for completing payment transaction can finish its job in average 1 sec. Average service time, S = 1 second. 


## The idea of averages

## USL

## Modeling

## Examples

## References
