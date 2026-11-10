# dsc

Repositorio base de los laboratorios del programa de **Data Science** (Credicorp × NTT DATA).
Cada equipo trabaja en su propio fork y entrega cada laboratorio con un pull request.

## Cómo empezar (una vez por equipo)

1. El capitán del equipo hace **fork** de este repositorio en su cuenta personal de GitHub, sin cambiarle el nombre.
   El portal detecta solo el fork `<capitan>/dsc`; no hay que registrar nada.
2. El capitán agrega a sus compañeros como colaboradores del fork.
3. Cada integrante clona el fork y trabaja ahí.

```bash
git clone https://github.com/<capitan>/dsc.git
cd dsc
```

## Cómo entregar un laboratorio

Cada laboratorio se entrega con un pull request del equipo. Basta con uno; si varios integrantes abren el suyo,
cuentan todos los válidos. Cada PR sale de una rama con este nombre:

```
l<N>_DSC_<CÓDIGO>
```

- `<N>`: el número del laboratorio (`l1`, `l2`, …).
- `<CÓDIGO>`: el código de participante de quien abre el PR (por ejemplo `01AA`).

Ejemplo para el Laboratorio 1:

```bash
git switch -c l1_DSC_01AA
# … trabajo del laboratorio …
git add -A
git commit -m "feat: laboratorio 1"
git push -u origin l1_DSC_01AA
```

En GitHub, abre el PR **dentro del fork del equipo**, con base `main` y compare `l1_DSC_01AA`, listo para revisión
(no en borrador). El portal lo detecta solo y lo valida contra la guía del laboratorio.

## Estructura

Cada laboratorio indica en su guía qué carpetas y archivos crear. Mantén el código y las notas del equipo en este
repositorio; los datos, credenciales y archivos `.env` no se suben.
