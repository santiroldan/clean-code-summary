# 🧹 Resumen del libro *Clean Code* (Robert C. Martin) — con ejemplos en TypeScript

Este resumen es una referencia exhaustiva de prácticas, principios, "code smells" y refactorizaciones, con ejemplos prácticos en TypeScript y recomendaciones para aplicar día a día.  

---

## 📋 Tabla de contenidos
1. [¿Qué es Clean Code?](#-1-qué-es-clean-code)
2. [Regla del Boy Scout](#-2-regla-del-boy-scout)
3. [Nombres: la base del código legible](#-3-nombres-la-base-del-código-legible)
4. [Funciones: diseño y patrones](#-4-funciones-diseño-y-patrones)
5. [Parámetros y tipos retornados](#-5-parámetros-y-tipos-retornados)
6. [Clases, objetos y SRP](#-6-clases-objetos-y-srp)
7. [Principios SOLID](#-7-principios-solid)
8. [Manejo de errores](#-8-manejo-de-errores)
9. [Tests: F.I.R.S.T.](#-9-tests-first)
10. [Code smells importantes](#-10-code-smells-importantes)
11. [Arquitectura y módulos](#-11-arquitectura-y-módulos)
12. [Prácticas y herramientas (lint, format, CI)](#-12-prácticas-y-herramientas-lint-format-ci)
13. [TypeScript: buenas prácticas](#-13-typescript-buenas-prácticas)
14. [Hoja de referencia rápida](#-14-hoja-de-referencia-rápida)
15. [Cómo aplicarlo día a día](#-15-cómo-aplicarlo-día-a-día)
16. [Ejemplo práctico: De sucio a limpio](#-16-ejemplo-práctico-de-sucio-a-limpio)
17. [Recomendaciones](#-17-recomendaciones)
---

## 🧹 1. ¿Qué es *Clean Code*?

Código limpio = código que:
- **Se lee fácil** y transmite intención.
- **Es simple, directo y con pocas sorpresas.**
- **Facilita cambios** (extensión y corrección) con bajo coste.
- Está **bien probado** y bien organizado en módulos/responsabilidades.

La ambición no es escribir código perfecto, sino **escribir código limpio constantemente**.

---

## 🏕️ 2. Regla del Boy Scout

**“Deja el campamento más limpio de lo que lo encontraste.”**

Aplicación práctica:
- Cada vez que tocas un archivo, arreglo pequeño: Renombrar variable, extraer una función, añadir test, quitar duplicación.
- No necesitas refactorizar todo el módulo en una PR enorme; **"Pequeñas mejoras incrementales son la vía"**.

---

## ✍️ 3. Nombres: la base del código legible

Reglas:
- **Revelan intención**: Nombres deben responder “qué” y “por qué”.
- Evita abreviaturas crípticas.
- Prefiere nombres pronunciables y consistentes.
- Para booleanos usa prefijos claros (`is`, `has`, `can`, `should`).
- Nombres de funciones deben ser verbos; nombres de tipos/clases sustantivos.

```ts
// Malo
const d = 86400;
function get(u) { /* ... */ }

// Bueno
const SECONDS_IN_A_DAY = 86400;
function getActiveUsers(): User[] { /* ... */ }
```

---

## 📦 4. Funciones: diseño y patrones

- Una sola responsabilidad por función.
- Corto (ideal < 20 líneas).
- Guard clauses para reducir anidamiento.
- Separar comandos (mutan estado) y consultas (devuelven datos).

```ts
// Malo
function sendWelcome(user?: User) {
  if (user) {
    if (!user.isBanned) {
      sendEmail(user.email, "Welcome!");
    }
  }
}

// Bueno
function sendWelcome(user?: User) {
  if (!user) return;
  if (user.isBanned) return;
  sendEmail(user.email, "Welcome!");
}
```

---

## 🎯 5. Parámetros y tipos retornados

- 0–2 parámetros es ideal (usar objeto cuando hay más).
- Evita flags booleanos que cambian comportamiento.

```ts
interface CreateUserProps { name: string; age: number; isAdmin?: boolean; }

function createUser(props: CreateUserProps) { /* ... */ }
```

---

## 🧱 6. Clases, objetos y SRP

- Cada clase tiene una razón para cambiar.
- Encapsula estado y comportamiento correctamente.
- Evita clases que hacen todo.

```ts
class UserValidator { validate(user: User) { /* ... */ } }
class UserRepository { save(user: User) { /* ... */ } }
class UserNotifier { notify(user: User) { /* ... */ } }
```

---

## ⚖️ 7. Principios SOLID

- S — Single Responsibility Principle.
- O — Open/Closed Principle.
- L — Liskov Substitution Principle.
- I — Interface Segregation Principle.
- D — Dependency Inversion Principle.

```ts
interface DiscountStrategy { apply(total: number): number; }
class NoDiscount implements DiscountStrategy { apply(total: number) { return total; } }
class BlackFridayDiscount implements DiscountStrategy { apply(total: number) { return total * 0.8; } }

function checkout(items: Item[], strategy: DiscountStrategy) {
  const total = sum(items);
  return strategy.apply(total);
}
```

---

## 🛡️ 8. Manejo de errores

- Excepciones > códigos de error.
- Manejar errores en el nivel adecuado.
- Para flujo funcional, usar Result / Either.

```ts
type Result<T, E = Error> = { ok: true; value: T } | { ok: false; error: E };

function parseJson<T>(s: string): Result<T> {
  try { return { ok: true, value: JSON.parse(s) as T }; }
  catch (err) { return { ok: false, error: err as Error }; }
}
```

---

## 🧪 9. Tests: F.I.R.S.T.

- Fast
- Independent
- Repeatable
- Self-validating
- Timely

```ts
describe("addTax", () => {
  it("adds tax correctly", () => {
    expect(addTax(100, 0.21)).toBe(121);
  });
});
```

---

## 🚨 10. Code smells importantes

- Funciones largas → extraer funciones.
- Clases grandes → aplicar SRP.
- Duplicación → extraer método.
- Switch largos → strategy.
- Primitive obsession → value objects.
- Comentarios innecesarios → refactoriza.

---

## 🏗️ 11. Arquitectura y módulos

- Diseñar módulos con API clara.
- Separar capas o usar vertical slices / hexagonal.
- Mantener dependencias hacia las abstracciones.

---

## ⚙️ 12. Prácticas y herramientas (lint, format, CI) 

- ESLint + Prettier.
- Husky + pre-commit.
- Type-check en CI.
- Cobertura mínima en código crítico.

---

## 💻 13. TypeScript: buenas prácticas

- strict: true en tsconfig.
- Evitar any; usar unknown + type guards.
- Usar readonly.
- Preferir discriminated unions.

```ts
type Shape =
  | { kind: 'circle'; radius: number }
  | { kind: 'rect'; width: number; height: number };

function area(s: Shape) {
  if (s.kind === 'circle') return Math.PI * s.radius ** 2;
  return s.width * s.height;
}
```

---

## ✅ 14. Hoja de referencia rápida 

- ¿Nombres revelan intención?
- ¿Función hace una sola cosa?
- ¿Hay duplicación que quitar?
- ¿Tests cubren el cambio?
- ¿Errores manejados correctamente?
- ¿Refactor pequeño (Boy Scout)?
- ¿Lint/format en verde?

---

## 🔁 15. Cómo aplicarlo día a día

- Refactorizar un poco en cada PR.
- PRs pequeños y atómicos.
- Code reviews centrados en intención.
- Practicar katas de código.

---

## 🧾 16. Ejemplo práctico: De sucio a limpio

```ts
// MAL
function processOrder(req: any) {
  if (!req.userId) throw new Error('missing');
  const total = req.items.reduce((s: number, i: any) => s + i.price, 0);
  db.save({ userId: req.userId, total });
  sendEmail(req.userId, `Total is ${total}`);
}

// BIEN
interface OrderRequest { userId: string; items: { price: number }[] }

function processOrder(req: OrderRequest) {
  const order = buildOrder(req);
  saveOrder(order);
  notifyOrderCreated(order);
}

function buildOrder(req: OrderRequest) {
  validateOrderRequest(req);
  const total = calculateTotal(req.items);
  return { userId: req.userId, items: req.items, total };
}
```

---

## 🎯 17. Recomendaciones

- Clean Code = hábito.
- Refactorizar continuamente.
- Tests = confianza para cambiar.
- Código legible acelera a largo plazo.
