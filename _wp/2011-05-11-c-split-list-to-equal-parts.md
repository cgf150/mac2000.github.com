---
layout: post
title: C# split list to equal parts
permalink: /579
tags: [.net, array, asp.net, c#, list, parts]
---

    /// <summary>

    /// Split any list to equal partitions
    /// </summary>
    /// <typeparam name="T"></typeparam>
    /// <param name="list"></param>
    /// <param name="totalPartitions"></param>
    /// <returns></returns>
    public static List<T>[] Partition<T>(List<T> list, int totalPartitions)
    {
        if (list == null)
            throw new ArgumentNullException("list");

        if (totalPartitions < 1)
            throw new ArgumentOutOfRangeException("totalPartitions");

        List<T>[] partitions = new List<T>[totalPartitions];

        int maxSize = (int)Math.Ceiling(list.Count / (double)totalPartitions);
        int k = 0;

        for (int i = 0; i < partitions.Length; i++)
        {
            partitions[i] = new List<T>();
            for (int j = k; j < k + maxSize; j++)
            {
                if (j >= list.Count)
                    break;
                partitions[i].Add(list[j]);
            }
            k += maxSize;
        }

        return partitions;
    }
