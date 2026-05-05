---
title: "Romulus=Quirinus and Summer Kiara Post-mortem"
slug: "romulus-quirinus-and-summer-kiara-post-mortem"
url: "/romulus-quirinus-and-summer-kiara-post-mortem/"
date: "2022-07-15T15:25:18.000Z"
lastmod: "2022-07-15T16:04:57.000Z"
draft: false
authors:
  - "officera"
ghost:
  id: "62d0c2ac623fd90001f6855d"
  uuid: "5fd93ffe-60c4-4d41-aa01-09e3537ee32f"
  status: "published"
  type: "post"
  visibility: "public"
---

This post will explore the timeline and cause of the recent NP bug of [Romulus=Quirinus](https://apps.atlasacademy.io/db/NA/servant/280/noble-phantasms) and [Summer Kiara](https://apps.atlasacademy.io/db/NA/servant/285/noble-phantasms) in the NA version. First, we will follow how this type of stacking Super Effective NP evolved in the Japanese version of the game. With this necessary context, we can then look into how the bug came to be in NA.

<strong>TL;DR:</strong> A mismatch between the game data and the game code led to Romulus and Summer Kiara checking for traits on themselves instead of on enemies for their Noble Phantasms' Super Effective damage conditions. The NA version updated the game code but didn't update the game data accordingly.

## JP server

Romulus=Quirinus was first released in JP on 16th April 2020 with a new stacking Super Effective NP that checked for how many traits existed on enemies. As seen in the following video, Romulus was working as intended after his release in JP:

- Exhibit 1: [https://youtu.be/5h6hKWD2T1s?t=107](https://youtu.be/5h6hKWD2T1s?t=107)  
Fight: GUDAGUDA 4 Rerun Challenge Quest  
Self SE Dmg: [266k-331k] [https://i.imgur.com/0Pw9Cvr.png](https://i.imgur.com/0Pw9Cvr.png)  
Enemy SE Dmg:  [414k-512k] [https://i.imgur.com/YqauPol.png](https://i.imgur.com/YqauPol.png)  
Actual Damage: 505k (within Enemy SE Dmg range)

There were [many parameters](https://apps.atlasacademy.io/db/JP/noble-phantasm/304201) in his NP but the parameter of concern here is <code>Target</code>. On release, the <code>Target</code> parameter of Romulus's NP was set to <code>0</code>. As far as we know, the <code>Target</code> parameter was unused in the game code1 at this point in time and <code>0</code> was just a dummy placeholder value2. Before version 2.26.0 (JP), when the game calculated the damage for stacking SE NP, it would always check for the existence of the trait(s) on enemies.

After Romulus, 2 other stacking SE NPs were released. The first one was [Miyamoto Musashi's upgraded NP](https://apps.atlasacademy.io/db/JP/servant/153/noble-phantasms) (9th August 2020) and the second one was [Summer Kiara's NP](https://apps.atlasacademy.io/db/JP/servant/285/noble-phantasms) (17th August 2020). Both of them also check for the existence of the trait(s) on enemies like Romulus's NP so no change was needed in the game code. Both of them used <code>0</code> as the dummy value for the <code>Target</code> parameter.

On <strong>20</strong>th January 2021, [Taira no Kagekiyo (Avenger)](https://apps.atlasacademy.io/db/JP/servant/303) was released and she also used the stacking Super Effective NP. However, instead of checking for existence of the Vengeance trait on enemies, Taira's NP looks for the existence of the trait/buff on herself. Thus, a new app version, 2.26.0 (JP), was also released to deal with this new condition.

To delineate whether the stacking SE NP checks on self or enemies for the existence of the trait(s), DW made use of the hitherto unused <code>Target</code> parameter. It was decided that <code>Target=0</code> would mean checking on self and <code>Target=1</code> would mean checking on enemies.

As noted above, Romulus, Musashi and Summer Kiara all check for traits' existence on enemies. However, they all had <code>Target=0</code> as originally released. Therefore, together with the 2.26.0 update, all of their <code>Target</code> values were changed to <code>1</code> in accordance with the change in the game code.

## NA Server

Back at NA server, Romulus=Quirinus was released in the same manner as they did in JP, on 30th March 2022. The <code>Target</code> value was set to <code>0</code> and his NP was working as intended as seen in the video below:

- Exhibit 2: [https://youtu.be/qPJAp-vCVqM?t=138](https://youtu.be/qPJAp-vCVqM?t=138)  
Fight: Olympus Main Quest "Treasured Beast" Boss Fight  
Self SE Dmg: [47k-51k] [https://i.imgur.com/LD4qzpO.png](https://i.imgur.com/LD4qzpO.png)   
Enemy SE Dmg: [63k-77k] [https://i.imgur.com/tLyp4jG.png](https://i.imgur.com/tLyp4jG.png)   
Actual Damage: 63k (within Enemy SE Dmg range)

Thus, we could confirm that Romulus=Quirinus was released together with the original stacking SE NP game code that only checked for the existence of the trait on enemies. Romulus=Quirinus was released with the game code that <strong>didn't use</strong> the <code>Target</code> parameter (pre-Taira game code).

Between Romulus=Quirinus's release and July 13th, it was noticed that Romulus's NP was dealing lesser than expected damage. There were a couple isolated posts that noticed this change, but no action was ever taken since the wider community was oblivious to this change. Further notice was raised when Summer Kiara also suffered from the same bug.

Given that Romulus=Quirinus's NP was working correctly on release, something must have changed to cause it to break. Since Atlas Academy tracks every game data change, we know that Romulus's NP had maintained <code>Target=0</code> from release to the bugfix on July 13th. Since the game data didn't change but the damage was different, the only possible culprit would be the game code.

Following the various Romulus=Quirinus gameplay videos, we could narrow down the window when the game code was changed, leading to lower than expected NP damage:

- <strong>GUDAGUDA 4 Rerun</strong>  
Exhibit 3: [https://youtu.be/6ZkjsTb0wsk?t=160](https://youtu.be/6ZkjsTb0wsk?t=160)  
Fight: GUDAGUDA 4 Rerun Challenge Quest  
Self SE Dmg: [288k - 365k] [https://i.imgur.com/jRByaHN.png](https://i.imgur.com/jRByaHN.png)  
Enemy SE Dmg: [639k - 794k] [https://i.imgur.com/7OfoJrx.png](https://i.imgur.com/7OfoJrx.png)  
Actual Damage: 740k (within <strong>Enemy SE Dmg</strong> range)
- <strong>Fate/Requiem collab</strong>  
Exhibit 4: [https://youtu.be/gn7J6DMdFuc?t=135](https://youtu.be/gn7J6DMdFuc?t=135)  
Fight: Fate/Requiem Dark Marie Fight  
Self SE Dmg: [63k - 77k] [https://i.imgur.com/Ww9cXIm.png](https://i.imgur.com/Ww9cXIm.png)  
Enemy SE Dmg: [84k - 103k] [https://i.imgur.com/Wz1Cl6S.png](https://i.imgur.com/Wz1Cl6S.png)  
Actual Damage: 92k (within <strong>Enemy SE Dmg</strong> range)
- <strong>Summer 4 Rerun</strong>  
Exhibit 5: [https://youtu.be/qbHfbhcdm5A?t=279](https://youtu.be/qbHfbhcdm5A?t=279)  
Fight: Summer 4 Rerun Jeanne Sisters Fight  
Self SE Dmg: [67k - 82k] [https://i.imgur.com/6zz7kOW.png](https://i.imgur.com/6zz7kOW.png)  
Enemy SE Dmg: [78k - 95k] [https://i.imgur.com/g8LuMB5.png](https://i.imgur.com/g8LuMB5.png)  
Actual Damage: 73k (within <strong>Self SE Dmg</strong> range)

We can see that Romulus's NP stopped working as expected between the Fate/Requiem event and the Summer 4 rerun event. Between those two events, Aniplex released version 2.31.0 (NA) on May 23rd. Version 2.31.0 introduced new exciting features such as Servant coins, Grailing to lv120, Append skills.

In the JP server, servant coins, grailing to lv120 and append skills were introduced after Taira's release. Therefore, it is safe to assume that together with the new headline features, version 2.31.0 of the NA region also merged the <strong>new stacking SE NP code that was introduced with Taira no Kagekiyo</strong>.

As noted above in the JP version, when the Taira no Kagekiyo update hit, Romulus's NP's <code>Target=0</code> was required to be changed to <code>Target=1</code>. Lasengle overlooked this change in NA however, and Romulus=Quirinus's NP still had <code>Target=0</code> despite NA now using post-Taira game code which took the <code>Target</code> parameter into account.

> As a result, Romulus=Quirinus was checking for Roman buffs on himself instead of enemies after the append skill update on NA.

Summer Kiara was released with pre-Taira data <code>Target=0</code> as well, so she also suffered from the same bug as Romulus. Interestingly, Musashi's NP upgrade was released with <code>Target=1</code> so her new NP worked as intended right from the start in NA.

On July 13th, Lasengle deployed a hotfix, updating the game data and changing Romulus and Summer Kiara's NPs' <code>Target</code> to <code>1</code>. As of today, Romulus=Quirinus and Summer Kiara behave exactly like JP server.

I sincerely thank the people over at the Discord who have crunched the numbers to understand this issue, everyone who have reported and amplified it, and the video creators so I could create this thread with publicly accessible evidence.

Thank you for reading,  
OfficerA

<strong>1:</strong> Note that while the stacking SE NP didn't use <code>Target</code> at the time, other NP types did make use of the parameter.

<strong>2: Why would DW add an unused</strong> <code>Target</code> parameter? For other Super Effective NPs, <code>Target</code> is used to determine the trait that would trigger the SE damage. When DW was coding for this NP SE stack function, we guess that they already had Musashi's upgraded NP in mind. Since Musashi's upgraded NP targets 2 traits: [109 - Alter Ego] and [115 - Moon Cancer], <code>TargetList</code> was used and Target became unused.

<strong>Couldn't DW have removed</strong> <code>Target</code> instead of having it with a dummy value? Due to how the parameters are parsed, there would be less code change with a dummy value than not having a <code>Target</code> value.
