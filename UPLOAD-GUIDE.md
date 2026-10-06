# The Green Table — upload guide

This package is the whole website as static files. There is no server code, no
database and nothing to install. Upload it and it runs.

Total size: about 23 MB, of which roughly 20 MB is photography and film.

---

## 1. Upload it

The **contents** of this folder go into your web root — usually `public_html` on
cPanel, or `www` / `httpdocs` on other hosts. Not the folder itself, its contents,
so that `index.html` sits directly in the web root.

Two ways:

- **cPanel File Manager** — open `public_html`, click Upload, choose the zip,
  then use *Extract* on it once it has uploaded.
- **FTP** — copy everything across. Use **binary** transfer mode, not ASCII.

Afterwards the structure should look like this:

```
public_html/
  index.html          <- the home page
  .htaccess           <- headers, caching, 404 (hidden file; make sure it copied)
  404.html
  robots.txt
  sitemap.xml
  about/      index.html
  contact/    index.html
  cookies/    index.html
  events/     index.html
  experience/ index.html
  gallery/    index.html
  menu/       index.html
  privacy/    index.html
  reserve/    index.html
  assets/     (styles, scripts, fonts used by the code)
  brand/      (logo artwork — also the favicon and share image)
  fonts/      (Bodoni Moda and Montserrat, self-hosted)
  media/      (all photography and film)
```

`.htaccess` is a hidden file. In cPanel File Manager turn on *Settings → Show
Hidden Files* or you may not see that it arrived.

---

## 2. Two things to do before it goes live

**a. Put your domain into two files.** Both currently say
`REPLACE-WITH-YOUR-DOMAIN`. Search and replace it in:

- `sitemap.xml`
- `robots.txt`

If you skip this, search engines are handed a sitemap pointing at nothing.

**b. Turn on HTTPS, then turn on HSTS.** Get your certificate first (cPanel does
this free via AutoSSL). Once the site answers on `https://`, open `.htaccess` and
uncomment the `Strict-Transport-Security` line. It is commented out on purpose:
switching it on before TLS works will lock visitors out of the http version.

---

## 3. The forms do not work in this package, by design

This is the one real limitation, and it is worth understanding rather than
discovering.

The reservation form and the event enquiry form need somewhere to send their
data. In this package there is no server, so there is nothing to receive them.
Rather than let a visitor fill in a booking request and watch it fail, **the
forms have been replaced with a short notice** saying that online requests are
not open yet and to contact the restaurant directly.

You have three options:

1. **Leave it as it is** until the restaurant's phone and email are decided, then
   send them to me and I can wire the forms to a form handler.
2. **Point the forms at a form service** such as Formspree, Basin or Netlify
   Forms, which accepts a POST and emails you. This needs a small code change
   rather than a setting.
3. **Host the full application instead**, which keeps both forms working exactly
   as they do now, including writing to a database. That needs a host that runs
   Cloudflare Workers plus a database, rather than plain file hosting.

Whichever you choose, tell me and I will prepare it.

---

## 4. One thing that is not in this package

The hero film, the 35 MB 4K video at the top of the home page, is **not** in
these files. It is streamed from a separate address because the hosting platform
caps any single file at 25 MB and the film is larger than that. The page points
at that address directly, so the film plays normally — but it does mean the home
page depends on an external link that is outside your hosting. Say the word if
you would rather it lived on your own server; the film would need re-encoding to
fit under 25 MB, or splitting.

---

## 5. Replacing photographs later

Photographs keep stable filenames, which is convenient but means a browser or a
CDN may keep serving the old version after you replace a file. **When you swap an
image, give it a new filename** and update the reference, rather than
overwriting. Otherwise people will keep seeing the old photograph until their
cache expires.

The whole image list, with the size and crop each slot expects, is in the
project's `media-manifest.md`.

---

## 6. Where the extras came from

| File | What it does |
| --- | --- |
| `.htaccess` | Security headers, compression, caching and the 404 page. Every rule is wrapped so a missing Apache module is skipped instead of breaking the site. |
| `404.html` | A branded page for wrong addresses, matching the site's typography and colours. |
| `robots.txt` | Lets search engines index the site, points at the sitemap. |
| `sitemap.xml` | The list of pages for search engines. |

These are kept in the project's `app/hosting/` folder. To rebuild this package
after any change to the site, run:

```bash
cd app
bash hosting/build-package.sh
```

It refuses to produce a package if any page or key asset is missing, so a broken
build cannot be uploaded by accident.
