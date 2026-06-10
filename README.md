# Fred'isu 🎵

Le rhythm game de Freydiss — clone d'osu! **100% navigateur, un seul fichier, zéro dépendance**. Importe un MP3 ou un MP4 → la map est générée par analyse spectrale (basses/aigus, sections kiai) et **calée sur la grille de BPM détectée**. Déployable en glisser-déposer.

## Jouer

- **▶ Jouer la démo** : morceau synthwave généré procéduralement
- **🎵 Importer un MP3 / MP4** : audio (mp3, m4a, ogg, wav…) ou vidéo (mp4, mkv, webm — seule la piste audio est utilisée)
- Visée difficile ? Monte « **Aide à la visée** » dans ⚙ Options (hitbox élargie jusqu'à +50 %)

## Contrôles

| Touche | Action |
|---|---|
| Souris / `Z` / `X` | Taper les cercles, tenir les sliders, tourner les spinners |
| `Échap` | Pause / reprise |
| `Espace` | Passer l'intro |
| `Entrée` | Rejouer (écran de résultats) |

## Mods

- **HD** (Hidden) : les cercles disparaissent avant le hit — score ×1,06
- **HR** (Hard Rock) : AR/OD augmentés, cercles plus petits — ×1,06
- **DT** (Double Time) : musique ×1,5 — ×1,12
- **FL** (Flashlight) : vision réduite autour du curseur — ×1,12 (désactivé sur mobile)

Le retry redonne **exactement la même map** (RNG seedé) : entraîne-toi !

## Leaderboard mondial (Supabase)

1. Crée un projet sur [supabase.com](https://supabase.com), exécute le SQL de création de la table `osu_scores` (voir ci-dessous)
2. Renseigne `SUPABASE_URL` et `SUPABASE_ANON_KEY` en tête du `<script>` dans `index.html`
3. C'est tout — appels REST directs, pas de SDK. Si les constantes sont vides, le classement mondial est masqué et le jeu reste 100% fonctionnel.

```sql
create table public.osu_scores (
  id bigint generated always as identity primary key,
  pseudo text not null check (char_length(pseudo) between 2 and 16),
  song text not null,
  diff smallint not null,
  mods text not null default '',
  score integer not null check (score >= 0),
  acc numeric(5,2) not null,
  max_combo integer not null,
  grade text not null,
  created_at timestamptz default now()
);
alter table public.osu_scores enable row level security;
create policy "insert_anon" on public.osu_scores for insert to anon with check (true);
create policy "select_anon" on public.osu_scores for select to anon using (true);
```

La clé **anon** est publique par design : la sécurité vient des policies RLS (insert/select uniquement).

## Déploiement

Projet 100% statique : Vercel, Netlify, GitHub Pages ou n'importe quel hébergeur de fichiers. Aucun build.
