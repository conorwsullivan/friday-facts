---
layout: post
title:  "New Cities Friday Facts #3: Roads to Prosperity"
date:   2019-10-11 00:00:00 -0700
#categories: new-cities friday-facts transportation
---

"Research<sup>[&#91;1&#93;][1]</sup> has shown that ease of access to transport has a stronger influence on whether someone will earn more than their parents did than the level of crime in their area or whether they grew up in a two- parent household," according to [this great video] by Wendover.

New Cities features many kinds of roads, from tree lined boulevards to high speed expressways to traffic-corralling one way streets, from stop signs to stop lights to full-stack interchanges. But currently the only kind of transportation in New Cities is private automobiles and freight trucks. There are no buses, bikes, pedestrians, trains, airplanes, boats, or even carpooling -- yet. After the crowdfunding champaign, there will be a contributors-only poll to determine which features to implement next, and most of the options will be related to transportation -- so don't worry, your inner train geek will be satisfied. In order to lay the groundwork for the many modes of transit, I needed to refactor and improve "the graph."

{% include image.html class="caption-below"
  link="https://xkcd.com/2036/"
  url="/blog/assets/images/edgelord.png"
  description="XKCD 2036" %}

The graph is the internal representation of the road system. In graph theory speak, the intersections and junctions are "nodes" and the road segments connecting them are "edges". Each edge connects two nodes, and each node to can connect up to six edges. Currently, there are only roads and expressways, but in the future this system might represent train tracks, bus routes, airways, and sea lanes. Underneath the graph is the lane system, which tells vehicles where to go. Lanes are also used for routing. The lane system is managed automatically by the game based on modifications of the graph. Sidewalks, bike lanes and HOV lanes would be implemented as special kinds of lanes on roads and expressways.

When you place an edge (road), the game detects any place where that edge crosses another. It then "splits" both edges by placing a node (intersection) between them. There might be many splits when placing one segment of road. But it gets even more complicated. The roads are split in order to conform to the elevation of terrain. There is a planner mode, allowing you to plan roads before paying for them, and this means that splits must keep track of which roads are completed or just planned. Once the splits are all resolved, intersections must be geometrically arranged, illegal states must be detected (underwater, too steep, etc), costs must be computed, terrain must be leveled, buildings must also be leveled, buildings and lots must be reassigned, new zone-able lots must be allocated, lanes must be updated, and everything has to be rendered to screen.

I have to do all of this in less than 16.667 milliseconds.

The graph was the very first thing I implemented, and therefore was most in need of a refactor. There were three issues I addressed with this refactor. Firstly, there were performance issues. I improved performance by making sure that things like rendering happened only once per frame, not once per split. (Performance issues are usually caused by redudant work.)

Secondly, it was possible to accidentally create duplicate, overlapping road segments (two roads connecting the same two intersections). This not only looked ugly but caused further glitches. Now when you overlap one road with another, it upgrades or modifies that section of road instead. In addition, building-by-overlap corrects the direction of one-way roads and expressways, meaning you can change the direction of a long many-intersection-ed stretch of one-way road using just two clicks -- a really powerful tool.

Thirdly, before you completed the road, it was difficult to predict where intersections would happen. Sometimes roads would go under or over eachother unexpectedly. Other times two roads would connect when what you actually wanted was an overpass. This was particularly frustrating when building expressways. Now the game computes and displays splits as you're placing the road, making connection points clear. It also computes and displays an accurate price tag for the road you're about to place.

Notice that I didn't talk much about the vehicles on the road, or the system of finding routes between locations on the graph (the router).  Vehicle physics and routing are two of the biggest performance issues right now. Also, vehicles often jam up due to bugs in the simulation. I'm planning to make improvements to the vehicle system next week, so New Cities Friday Facts #4 will probably be on that topic. Routing improvements will come later.

Transportation is really at the core of New Cities, and building roads is part of the [core gameplay loop]. I'm very glad that I've made these improvements. This project is moving forward at HOV lane speeds, so I'm thankful that you're riding carpool with me.

If you're excited about the project, join the [discord] or [subreddit].

[subreddit]: https://www.reddit.com/r/New_Cities
[discord]: https://discord.gg/udgeB2E
[this great video]: https://youtu.be/fwjwePe-HmA
[1]: https://nascsp.org/wp-content/uploads/2018/02/issuebrief-benefitsofruralpublictransportation.pdf
[core gameplay loop]: https://gameanalytics.com/blog/how-to-perfect-your-games-core-loop.html