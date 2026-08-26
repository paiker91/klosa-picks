# Registro público de pronósticos — Klosa

Cada pronóstico de este repositorio se anota **antes** de que empiece el partido
y su línea de cierre la captura después un proveedor de cuotas, no una persona.

Herramienta que lo calcula: **https://klosa-five.vercel.app**

## Cómo comprobarlo usted mismo

No hace falta fiarse de nadie. Hay tres cosas que puede verificar:

**1. El historial de commits.** Cada commit añade un pronóstico. La fecha en
que GitHub recibió el push no la controla quien escribe, así que si un pick
aparece después del partido, se ve.

```bash
git log --format="%ad  %s" --date=iso
```

**2. El sello de contenido.** El campo `id` de cada pick es el hash SHA-256 de
sus propios campos (`registradoEn deporte eventoId comienzo mercado lado
cuotaTomada`, separados por espacios, primeros 16 caracteres). Si alguien
cambiara una cuota a posteriori, el sello dejaría de cuadrar.

**3. Los ficheros son de solo-añadir.** `picks.jsonl` solo crece. Cualquier
diff que no sea "líneas nuevas al final" merece una mirada:

```bash
git log -p --follow picks.jsonl
```

Los cierres van en un fichero aparte a propósito: si se escribieran dentro de
la línea del pick, cada captura sería una modificación y un pick retocado se
confundiría con una captura normal.

## Formato

`picks.jsonl` — un pronóstico por línea:

| Campo | Qué es |
|---|---|
| `id` | Sello de contenido |
| `registradoEn` | Cuándo se anotó. **Siempre anterior a `comienzo`** |
| `comienzo` | Cuándo empieza el partido |
| `lado` | Lado apostado, con la etiqueta exacta del proveedor |
| `cuotaTomada` | La cuota que se cogió |

`cierres.jsonl` — la línea de cierre capturada, referida por `pickId`.

`resultados.jsonl` — el desenlace, sacado del marcador final.

`renuncias.jsonl` — picks cuyo cierre no se pudo capturar y no se va a poder.
Ocurre cuando la línea se mueve y el lado apostado ya no existe en el mercado
de cierre: un *Over 2* cuando el mercado cerró en 2.25 no tiene contra qué
compararse. Esos picks siguen en el registro, siguen contando en el yield si
se resolvieron, y no tienen CLV. Aparecen aquí en vez de desaparecer callando,
que es lo que haría alguien interesado en que su número saliera mejor.

## Sobre lo que este registro NO dice

Un CLV positivo no garantiza beneficio, y una muestra pequeña no dice nada en
absoluto. Por debajo de 100 apuestas ningún resultado de aquí es concluyente,
por bueno que parezca — eso es el tamaño de la muestra, no pesimismo.

Cuando la muestra no da para concluir, se dice. Es el motivo por el que existe
este registro.
