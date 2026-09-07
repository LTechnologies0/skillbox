# Skillbox

Collection de **Cursor Agent Skills** que je partage volontairement.

Chaque skill est un dossier autonome avec un `SKILL.md` (et éventuellement scripts / docs). Tu peux les installer localement ou les fork / cloner pour les réutiliser.

## Structure

```
skills/
  nom-du-skill/
    SKILL.md          # obligatoire
    reference.md      # optionnel
    scripts/          # optionnel
```

## Installer un skill

Copier ou lier le dossier du skill vers tes skills personnels Cursor :

```bash
# copie
cp -R skills/nom-du-skill ~/.cursor/skills/

# ou symlink (reste à jour avec le repo)
ln -s "$(pwd)/skills/nom-du-skill" ~/.cursor/skills/nom-du-skill
```

Pour un projet précis uniquement :

```bash
mkdir -p .cursor/skills
cp -R /chemin/vers/skillbox/skills/nom-du-skill .cursor/skills/
```

## Skills disponibles

| Skill | Description |
|-------|-------------|
| [ultra-light-gui](skills/ultra-light-gui/) | Règle d’or + **100** principes, garde-fous anti-régression UI, checklist profiling multi-stack |

## Ajouter un skill

1. Créer `skills/<nom>/SKILL.md` (nom en minuscules, tirets, ≤ 64 caractères).
2. Frontmatter YAML avec `name` + `description` (quoi + quand l’utiliser).
3. Instructions concises ; détails dans des fichiers liés au même niveau.
4. Ne jamais mettre de secrets, clés, ni données personnelles.

Voir [skills/_template/SKILL.md](skills/_template/SKILL.md) pour un modèle.

## Licence / partage

Ces skills sont destinés à être partagés. Si tu contributes, assume que le contenu peut être redistribué. Adapte la licence du repo si besoin.
