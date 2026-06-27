---
title: "Nextzy Gamification GameService"
type: wiki-entry
project: "nextzy-gamification"
createdAt: 2026-06-28T02:17:00
modifiedAt: 2026-06-28T02:17:00
target_agents:
  - backend
related_tools:
  - nestjs
  - prisma
tags:
  - snippet
  - database-transactions
  - service
---

# Nextzy Gamification `game.service.ts`

> **🤖 Agent Routing:** Backend agents should read this to understand how Prisma `$transaction` is used to ensure data integrity during the gacha spin.

> **🧠 LLM Context:** This NestJS service manages the core gacha mechanic. It calculates points and saves them to two tables (`User` and `SpinHistory`) simultaneously using a transaction.

## Code Snippet

```typescript
import { Injectable, NotFoundException } from '@nestjs/common';
import { PrismaService } from '../prisma/prisma.service';

@Injectable()
export class GameService {
  constructor(private prisma: PrismaService) {}

  async spin(userId: string) {
    const user = await this.prisma.user.findUnique({
      where: { id: userId },
    });

    if (!user) {
      throw new NotFoundException('User not found');
    }

    // RNG Logic: Randomly select points
    const pointsList = [300, 500, 1000, 3000];
    const rewardPoints = pointsList[Math.floor(Math.random() * pointsList.length)];

    // Database Transaction: Ensure both tables update together
    const result = await this.prisma.$transaction(async (tx) => {
      
      // 1. Increment the user's total points atomically
      const updatedUser = await tx.user.update({
        where: { id: userId },
        data: {
          totalPoints: {
            increment: rewardPoints,
          },
        },
      });

      // 2. Append to the spin history
      const history = await tx.spinHistory.create({
        data: {
          userId: userId,
          pointsReceived: rewardPoints,
        },
      });

      return { totalPoints: updatedUser.totalPoints, history };
    });

    return {
      message: 'Spin successful!',
      rewardPoints: rewardPoints,
      currentTotalPoints: result.totalPoints,
    };
  }
}
```

## 🔗 Related Context
- [[nextzygame-api]]
- [[nextzygame-database-protection]]
