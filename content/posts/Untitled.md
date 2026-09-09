---
title: "{{tp.file.title}}"
date: 2026-09-09T23:30:00+08:00
draft: false
tags:
  - 测试
categories: []
---

# 一

> 示例：
> **比起对比行为不端**
> ```
#import { z } from 'zod';

import bcrypt from 'bcryptjs';

import type { FastifyPluginAsync } from 'fastify';

import { prisma } from '../lib/prisma.js';

  

const registerBody = z.object({

email: z.string().email(),

name: z.string().min(1).max(100),

password: z.string().min(8),

phone: z.string().min(5).max(20).optional(),

});

  

const loginBody = z.object({

email: z.string().email(),

password: z.string(),

});

  

const refreshBody = z.object({

refreshToken: z.string(),

});

  

const profileBody = z.object({

name: z.string().min(1).max(100),

phone: z.string().min(5).max(20).optional().default(''),

});

  

const toPublicUser = (u: {

id: string;

email: string;

name: string;

role: string;

phone: string | null;

}) => ({ id: u.id, email: u.email, name: u.name, phone: u.phone, role: u.role });

  

export const authRoutes: FastifyPluginAsync = async (app) => {

app.post('/register', async (request, reply) => {

const parsed = registerBody.safeParse(request.body);

if (!parsed.success) return reply.code(400).send({ error: parsed.error.flatten() });

  

const { name, password } = parsed.data;

const email = parsed.data.email.toLowerCase();

const phone = parsed.data.phone || null;

const existing = await prisma.user.findUnique({ where: { email } });

if (existing) return reply.code(409).send({ error: 'Email already registered' });

  

const hashed = await bcrypt.hash(password, 10);

const user = await prisma.user.create({

data: { email, name, phone, password: hashed, role: 'USER' },

});

  

const accessToken = app.signAccessToken({ sub: user.id, role: user.role });

const refreshToken = app.signRefreshToken({ sub: user.id, role: user.role });

return reply.code(201).send({

user: toPublicUser(user),

accessToken,

refreshToken,

});

});

  

app.post('/login', async (request, reply) => {

const parsed = loginBody.safeParse(request.body);

if (!parsed.success) return reply.code(400).send({ error: parsed.error.flatten() });

  

const { password } = parsed.data;

const email = parsed.data.email.toLowerCase();

const user = await prisma.user.findUnique({ where: { email } });

if (!user) return reply.code(401).send({ error: 'Invalid credentials' });

  

const valid = await bcrypt.compare(password, user.password);

if (!valid) return reply.code(401).send({ error: 'Invalid credentials' });

  

const accessToken = app.signAccessToken({ sub: user.id, role: user.role });

const refreshToken = app.signRefreshToken({ sub: user.id, role: user.role });

return {

user: toPublicUser(user),

accessToken,

refreshToken,

};

});

  

app.post('/refresh', async (request, reply) => {

const parsed = refreshBody.safeParse(request.body);

if (!parsed.success) return reply.code(400).send({ error: 'Invalid token' });

  

try {

const decoded = app.jwt.verify<{ sub: string; role: string; type: string }>(

parsed.data.refreshToken,

);

if (decoded.type !== 'refresh') return reply.code(401).send({ error: 'Invalid token' });

  

const accessToken = app.signAccessToken({ sub: decoded.sub, role: decoded.role });

return { accessToken };

} catch {

return reply.code(401).send({ error: 'Invalid or expired token' });

}

});

  

app.get('/me', { onRequest: [app.authenticate] }, async (request) => {

const user = await prisma.user.findUnique({ where: { id: request.user.sub } });

if (!user) return { user: null };

return { user: toPublicUser(user) };

});

  

app.put('/profile', { onRequest: [app.authenticate] }, async (request, reply) => {

const parsed = profileBody.safeParse(request.body);

if (!parsed.success) return reply.code(400).send({ error: parsed.error.flatten() });

  

const { name } = parsed.data;

const phone = parsed.data.phone || null;

const user = await prisma.user.update({

where: { id: request.user.sub },

data: { name, phone },

});

return { user: toPublicUser(user) };

});

};
> ```
> ![图片描述](/images/R-C.jpg)
