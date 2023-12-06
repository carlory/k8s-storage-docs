---
title: Contribution Guidelines
weight: 10
description: Storage Special Interest Group
---

## Preflight

We love having folks help in any capacity! We recommend you start by reading the overall [Kubernetes contributor's guide](https://github.com/kubernetes/community/blob/master/contributors/guide)


## Meetings

*Joining the [developer mailing list](https://groups.google.com/forum/#!forum/kubernetes-sig-storage) for the group will typically add invites for the following meetings to your calendar.*
* Regular CSI Implementation meeting: [Monday and Wednesdays at 10:00 PT (Pacific Time)](https://zoom.us/j/614261834) (weekly). [Convert to your timezone](http://www.thetimezoneconverter.com/?t=10:00&tz=PT%20%28Pacific%20Time%29).
  * [Meeting notes and Agenda](https://docs.google.com/document/d/1_hvq3nleqQEYatH9V_Gfep39jMzaFJRSN2ioA0PFq-Q/edit#).
* Regular SIG Meeting: [Thursdays at 9:00 PT (Pacific Time)](https://zoom.us/j/614261834) (biweekly). [Convert to your timezone](http://www.thetimezoneconverter.com/?t=9:00&tz=PT%20%28Pacific%20Time%29).
  * [Meeting notes and Agenda](https://docs.google.com/document/d/1-8KEG8AjAgKznS9NFm3qWqkGyCHmvU6HVl0sk5hwoAE/edit?usp=sharing).
  * [Meeting recordings](https://www.youtube.com/watch?v=Eh7Qa7KOL8o&list=PL69nYSiGNLP02-BMqJdfFgGxYQ4Nb-2Qq).
* Regular SIG Issue Triage meeting: [Tuesdays at 10:00 PT (Pacific Time)](https://zoom.us/j/614261834) (weekly). [Convert to your timezone](http://www.thetimezoneconverter.com/?t=9:00&tz=PT%20%28Pacific%20Time%29).
  * [Meeting notes and Agenda](https://docs.google.com/document/d/1n-dXXvCbHsPfO1yrKwT1qoC80KhsxHYKbRdChdzqeXY/edit#)

## We're working on

* [Planning spreadsheet](https://docs.google.com/spreadsheets/d/1t4z5DYKjX2ZDlkTpCnp18icRAQqOE85C1T1r2gqJVck/edit#gid=604003098)
* [Kubernetes Enhancements](https://github.com/kubernetes/enhancements/issues?q=is%3Aopen+is%3Aissue+label%3Asig%2Fstorage)
* [Kubernetes Issue Triage](https://github.com/orgs/kubernetes/projects/146)
* [Test Grid](https://testgrid.k8s.io/sig-storage-csi-ci)

## Ramping up on Kubernetes Storage

Keep in mind that these artifacts reflect the state of the art at the time they were created. The Kubernetes project is constantly evolving, and these artifacts may not reflect the current state of the art. 

| Date | Title | Link | Description |
| --- | --- | --- | --- |
| 2023 January 23 | PV/PVC Controller Deep Dive | [Slides](https://docs.google.com/presentation/d/1XI_XzxlAYF6dqRfQQsoBd-UWRHssH2Ts5KwK5NDIMgg/edit) | A walk through of the PV controller source code following the Dynamic Volume Provisioning with CSI scenario |
| 2022 November 9 | Kubernetes SIG Storage Deep Dive | [Video](https://www.youtube.com/watch?v=_XXn3-yDZA0) | An overview and update of SIG Storage by Xing Yang, VMware & Mauricio Poppe, Google at KubeCon NA 2022. |
| 2022 May 16 | Kubernetes SIG Storage Deep Dive | [Video](https://www.youtube.com/watch?v=dsEeQqRSg74) | An overview and update of SIG Storage by Xing Yang, VMware & Jan Šafránek, Red Hat at KubeCon Europe 2022. |
| 2020 November 20 | Intro & Deep Dive: Kubernetes SIG-Storage | [Video](https://www.youtube.com/watch?v=rnCdvWToPPM&t=2s) | An overview and update of SIG Storage by Xing Yang and Michelle Au at KubeCon/CloudNativeCon NA 2020. |
| 2020 November 20 | Intro & Deep Dive: Kubernetes SIG-Storage | [Video](https://www.youtube.com/watch?v=rnCdvWToPPM&t=2s) | An overview and update of SIG Storage by Xing Yang and Michelle Au at KubeCon/CloudNativeCon NA 2020. |
| 2020 November 20 | Intro & Deep Dive: Kubernetes Data Protection WG | [Video](https://www.youtube.com/watch?v=g8HEQnLVo04) | An overview of Data Protection WG by Xing Yang and Xiangqian Yu at KubeCon/CloudNativeCon NA 2020. |
| 2020 November 18 | Beyond File and Block Storage in Kubernetes | [Video](https://www.youtube.com/watch?v=Y3GgJb71Cwo) | An introduction of Container Object Storage Interface (COSI) by Sidhartha Mani at KubeCon/CloudNativeCon NA 2020. |
| 2019 May 23 | Kubernetes Storage 101 | [Video](https://www.youtube.com/watch?v=_qfSzrPn9Cs) | An overview of usage of persistent storage in Kubernetes by David Zhu and Jan Šafránek at KubeCon/CloudNativeCon EU 2019.|
| 2018 May 03 | SIG Storage Intro | [Video](https://www.youtube.com/watch?v=GvrTl2T-Tts&list=PLj6h78yzYM2N8GdbjmhVU65KYm_68qBmo&index=164&t=0s) | An overview of SIG Storage By Saad Ali at KubeCon/CloudNativeCon EU 2018. |
| 2018 May 04 | Kubernetes Storage Lingo 101 | [Video](https://www.youtube.com/watch?v=uSxlgK1bCuA&t=0s&index=300&list=PLj6h78yzYM2N8GdbjmhVU65KYm_68qBmo) | An overview of various terms used in Kubernetes storage and what they mean by Saad Ali at KubeCon/CloudNativeCon EU 2018.|
| 2017 May 18 | Storage Classes & Dynamic Provisioning in Kubernetes |[Video](https://youtu.be/qktFhjJmFhg)| Intro to the basic Kubernetes storage concepts for users (direct volume reference, PV/PVC, and dynamic provisioning). |
| 2017 March 29 | Dynamic Provisioning and Storage Classes in Kubernetes |[Blog post](https://kubernetes.io/blog/2017/03/dynamic-provisioning-and-storage-classes-kubernetes/)| Overview of Dynamic Provisioning and Storage Classes in Kubernetes at GA. |
| 2017 March 29 | How Kubernetes Storage Works | [Slides](https://docs.google.com/presentation/d/1Yl5JKifcncn0gSZf3e1dWspd8iFaWObLm9LxCaXZJIk/edit?usp=sharing) | Overview for developers on how Kubernetes storage works for KubeCon/CloudNativeCon EU 2017 by Saad Ali
| 2017 February 17 | Overview of Dynamic Provisioning for SIG Apps | [Video](https://youtu.be/NXUHmxXytUQ?t=10m33s) | Overview of Storage Classes and Dynamic Provisioning for SIG Apps
| 2016 October 7 | Dynamic Provisioning and Storage Classes in Kubernetes |[Blog post](https://kubernetes.io/blog/2016/10/dynamic-provisioning-and-storage-in-kubernetes/)| Overview of Dynamic Provisioning and Storage Classes in Kubernetes at Beta. |
| 2016 July 26 | Overview of Basic Volume for SIG Apps | [Video](https://youtu.be/DrLGxkFdDNc?t=11m19s) | Overview of Basic Volume for SIG Apps |
| 2016 March 25 | The State of State | [Video](https://www.youtube.com/watch?v=jsTQ24CLRhI&index=6&list=PLosInM-8doqcBy3BirmLM4S_pmox6qTw3) | The State of State at KubeCon/CloudNativeCon EU 2016 by Matthew Bates |
| 2016 March 25 | Kubernetes Storage 101 | [Video](https://www.youtube.com/watch?v=ZqTHe6Xj0Ek&list=PLosInM-8doqcBy3BirmLM4S_pmox6qTw3&index=38) | Kubernetes Storage 101 at KubeCon/CloudNativeCon EU 2016 by Erin Boyd |




