---
layout: post
title: A European solution for the UniFi Protect G4 Doorbell
comments: true
---

Having previously invested in a UniFi Protect surveillance infrastucture, I recently bought a UniFi G4 Doorbell from the States. In the pursuit of a proper set-up, I did a lot of googling, and found a lot of information, some of which brilliant, some useful, and some outright wrong. This post is meant as recap of my findings.

<!-- more -->

## Introduction

The UniFi G4 Doorbell is a brilliant piece of kit, well-integrated with the UniFi Protect video surveillance infrastructure. The platform allows you to store data locally, without a subscription and without the [creepiness that comes](https://www.theregister.com/2020/01/28/ring_data_sale/){:target="_blank"} [with Ring](https://www.theregister.com/2020/11/24/amazon_sidewalk_opt_out/){:target="_blank"}. This is also a double-edged sword though, since you can't run the doorbell stand-alone.

Like many smart doorbells, it connects to the network via WiFi, but takes power from an existing wired doorbell circuit. It is intended to be a drop-in replacement for a US doorbell with chime, which normally run on 16V AC power. In Europe most doorbell circuits run on 8V AC power, which means that we need to do some electrical wizardry to get it to work.

My goal for this project was to achieve the widest compatibility with chimes, especially European 8V ones. Most articles that talk about smart doorbells for European applications use the [Honeywell Ding Dong 8-16V Chime](https://www.amazon.co.uk/gp/product/B0001NPYZ2/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=idave05-21&creative=6738&linkCode=as2&creativeASIN=B0001NPYZ2&linkId=0c397e33b7a19c8293eb4d6a45a35375){:target="_blank"}, and there don't seem to be many 16V alternatives.

## Credits

My main inspiration was [this article on lazyadmin.nl](https://lazyadmin.nl/home-network/unifi-protect-g4-doorbell-review/){:target="_blank"}. I invite you to read it, especially the instructions to assemble the doorbell and install it on a door. It gave me the idea of using a relay to decouple the high-voltage power circuit to the doorbell from the low-voltage chime circuit.

[This article](https://www.instructables.com/Making-a-Door-Bell-Chime-With-Integrated-Transform/){:target="_blank"} is also useful, even though it applies to the Nest Hello.

## Two common misconceptions about power

In looking for information, I found many people have common misconceptions about the doorbells and how to power them.

### You can use any AC adapter, provided that it's more powerful than the requirements of the doorbell

The [datasheet](http://ui.com/ds/uvc-g4-doorbell-ds){:target="_blank"} says the doorbell needs 16-24VAC, and has 12VA max power consumption. VA stands for volt-amperes, and it is a way to express apparent power in AC circuits.

Many adapters state their power in Amps, so understand what AC adapter to use, you need to look at the output voltage of the adapter, and divide the Volt-Amp value by the output voltage. So, for example, if you have a 16V adapter, you will need

<script type="text/javascript">
window.MathJax = {
  tex: {
    packages: ['base', 'ams', 'cancel']
  },
  loader: {
    load: ['ui/menu', '[tex]/ams', '[tex]/cancel']
  }
};
</script>
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
</script>

$$\frac{12 \bcancel{V} A}{16\bcancel{V}} = 0.75A$$

Ubiquiti's own [Power Supply](http://ui.com/ds/uvc-g4-doorbell-ps-ds){:target="_blank"} is 20V, 0.7A, so

$$20V \cdot 0.7A = 14VA$$

So, all you need is 12-14VA. Even if conventional wisdom is that you can use a higher-amp adapter to power your lower-amp device, that's not entirely true. If you use a more powerful adapter, you need to check that it is a *regulated* power adapter, i.e. that it regulates the output voltage depending on load. Many AC-DC adapters are regulated, but **most AC-AC adapters aren't**. With an unregulated adapter, if your load is lower than the maximum load, the output voltage will be higher than its rated voltage.

The lazyadmin.nl article uses a 24V/30VA Vemer VN319000 adapter. But when you're only using 14VA, the adapter will output much more than 24V. When I tested it, it was outputting 30.5V, risking damage to the doorbell! Vemer's own technical assistance team has confirmed that higher voltage under a lighter-than-rated load is normal.

{:refdef: style="text-align: center;"}
![image](/assets/img/posts_img/2020-12-28-a-european-solution-for-the-unifi-g4-doorbell/tester_highvolt_photo.jpg)
{: refdef}

### You need an AC adapter powerful enough to power both doorbell and chime at the same time

When you press the button on the UniFi Doorbell, the doorbell temporarily shuts its internal power, and gives all the power to the chime. This means that at any given time, you're powering either the chime, or the doorbell, but never both. Ubiquiti support has confirmed that this is exactly how it's meant to operate.

## Enough talking. How do I do this?

The lazyadmin.nl article uses a 24V AC adapter and a 24V relay. I wanted to use a lower-power adapter. The tolerance of unregulated AC adapters illustrated above precisely the reason why the doorbell takes between 16V and 24V. It's better to stick to the lower end of this range, e.g. 16-18V, because if you use a higher-power adapter, it will output more than 18V.

I bought:
- A [18V 0.8A AC-AC adaptor](https://www.acadaptorsrus.co.uk/18v-0-8a-ac-ac-transformer-adaptor-power-supply-18vac-800ma-14-4va/){:target="_blank"} from AC Adaptors R Us Ltd. Very solid product, runs cool, and outputs 14.4VA.
- A [Very Handy Little Relay](https://www.amazon.co.uk/gp/product/B00418XB6M/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B00418XB6M&linkCode=as2&tag=idave05-21&linkId=f4ddecd3ef40aa0e01dc17a60ed095b8){:target="_blank"}. This relay takes any input voltage between 6 and 28V, DC or AC.

You then have to connect everything like this:

{:refdef: style="text-align: center;"}
![image](/assets/img/posts_img/2020-12-28-a-european-solution-for-the-unifi-g4-doorbell/connection-schema.png)
{: refdef}

Note: the NO/NC contacts of the Very Handy Little Relay will behave exactly like your old doorbell button. As such, you can connect anything to them: a battery-powered chime, a chime with an external power supply, etc... If you have an existing doorbell circuit, you can connect those two contacts to the old circuit.

In my setup, I bought a [DC connector compatible with the above AC adapter](https://www.amazon.co.uk/gp/product/B07PDXG3BT/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B07PDXG3BT&linkCode=as2&tag=idave05-21&linkId=d699d99531f39651c4e408f68d88d595){:target="_blank"}, and put everything in a [suitable plastic box](https://www.amazon.co.uk/gp/product/B07RM6DXCY/ref=as_li_tl?ie=UTF8&camp=1634&creative=6738&creativeASIN=B07RM6DXCY&linkCode=as2&tag=idave05-21&linkId=f0adaa489304e00a9191975f78aeae58){:target="_blank"}. A bit of soldering here and there, and here is the result:

{:refdef: style="text-align: center;"}
![image](/assets/img/posts_img/2020-12-28-a-european-solution-for-the-unifi-g4-doorbell/inside_box.jpg)
{: refdef}

And here it is, in operation. Voltage reading at the doorbell is 18.8V. Since I haven't yet found a chime I like, I am using the multimeter as a chime.

<div style="text-align:center"><iframe width="420" height="236" src="http://www.youtube.com/embed/Z3jU7bkmJjY" frameborder="0" allowfullscreen></iframe></div>

## Other things/products I tried

This article was the result of *much* trial and error. Here is a list of other things I tried and why they didn't work for me.

| Thing | Why it didn't work |
| :------ |:--- |
| [This](https://www.amazon.co.uk/gp/product/B07VCNYQFB/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=idave05-21&creative=6738&linkCode=as2&creativeASIN=B07VCNYQFB&linkId=076edd97cf8b32a284314b85b3e6ad77){:target="_blank"} and [this other](https://www.amazon.co.uk/gp/product/B0815ZKPS7/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=idave05-21&creative=6738&linkCode=as2&creativeASIN=B0815ZKPS7&linkId=77ea1e4050dc707f4774334eb0dbca12){:target="_blank"} adapter | Both adapters were the same model. They were powerful enough, but they ran very hot and emanated a very chemical, burning-plastic smell. |
| [Vemer VN319000 30VA AC Adapter](https://www.amazon.co.uk/gp/product/B00F4QGVIA/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=idave05-21&creative=6738&linkCode=as2&creativeASIN=B00F4QGVIA&linkId=2aa6d554a41ba6232999b28aa9bb9cda){:target="_blank"} | As mentioned above, it was too powerful and output 30.5V. |
| [Vemer VN317400 15VA AC Adapter](https://www.amazon.co.uk/gp/product/B00F4QDJB2/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=idave05-21&creative=6738&linkCode=as2&creativeASIN=B00F4QDJB2&linkId=018b38450857a369a2e4b2a047e4d251){:target="_blank"} | Even though it's theoretically rated at 15VA, so it's perfect for this application, it still output 31V! |
| [PROTEK 16V 8VA BT8-16 AC Adapter](https://www.amazon.co.uk/gp/product/B07KZYK8QW/ref=as_li_qf_asin_il_tl?ie=UTF8&tag=idave05-21&creative=6738&linkCode=as2&creativeASIN=B07KZYK8QW&linkId=be99009754a492fd51f167970c3a2b83){:target="_blank"} | One of my initial attempts. Would power up the doorbell, but since it's rated 8VA it's probably too weak. |