---
layout: post
title: "XmlSerializer memory leaks in .NET 1.1"
excerpt: "Be careful with typed XmlSerializer"
tags: [.Net, C#, Coding]
comments: true
---

Yesterday after long hours and many many cups of coffee I finally identified memory leaks in managed code actually in .NET Framework (how it is possible at all?).
Symptom: If you create XmlSerializer using any other constructor than XmlSerializer(Type) for example  XmlSerializer(Type, Type[]), Framework will create new temporary assembly and load it into memory ( as we all know there is no way to unload assembly at all :) ). Taking in consideration that in our beta run we push about 1000 messages and got 1000 assemblies loaded it consumes all memory.
Solution: It is easy just cache your serializer in some singleton and enjoy.

UPD: Later I checked and Microsoft fixed this bug in .NET 2.0