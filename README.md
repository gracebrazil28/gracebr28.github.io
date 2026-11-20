# Radio Sky Journal

A personal radio astronomy log, published with GitHub Pages. Each entry is one Markdown file in `_posts/`. GitHub builds the site automatically, so nothing needs installing.
Preview: <https://gracebrazil28.github.io/radioskyjournal/>

## Folder layout

```
_config.yml              site title, author, your GitHub address  <- edit first
_posts/                  one Markdown file per journal entry
_layouts/                page templates (default, post, page)
_includes/trace.svg      the masthead line: the W3(OH) maser spectrum from entry 1
assets/css/style.css     the design
assets/images/<entry>/   figures, one folder per entry
assets/pdf/              full write-ups (PDF)
projects/<entry>/        code and README for each project
index.html               home page: the list of entries
about.md                 about page
```

## If you want to create your own github.io blog like mine, follow the following instructions:
### Publishing the site (first time)

1. **Create the repository.** On GitHub, create a new **public** repository named exactly `YOUR-USERNAME.github.io`, using your own GitHub username.
2. **Set your address.** In `_config.yml`, replace the three `YOUR-USERNAME` values with your username.
3. **Add your PDF.** Save the compiled report as `assets/pdf/w3oh-oh-maser-report.pdf`. The entry's download link uses that exact name. Then delete `assets/pdf/PUT-YOUR-PDF-HERE.md`.
4. **Upload the files.** Either:
   - **in the browser:** in the new repository, choose **Add file → Upload files**, drag in everything from this folder (keep the folder structure), and commit; or
   - **with git:**
     ```
     git clone https://github.com/YOUR-USERNAME/YOUR-USERNAME.github.io
     (copy these files into the cloned folder)
     git add .
     git commit -m "First entry: W3(OH) OH masers"
     git push
     ```
5. **Turn on Pages.** In the repository, go to **Settings → Pages**. Under **Build and deployment**, choose **Deploy from a branch**, branch `main`, folder `/ (root)`, and save.
6. **Check the build.** After a minute or two the site is live at `https://YOUR-USERNAME.github.io`. The **Actions** tab shows each build, and a red cross there means the build failed (the log says why).

If you'd rather keep this notebook in a repository with a different name (for example `radio-notebook`), set `baseurl: "/radio-notebook"` and the matching `url` in `_config.yml`. The site will then be at `https://YOUR-USERNAME.github.io/radio-notebook/`.

### Adding a new entry

1. Create a file in `_posts/` named `YYYY-MM-DD-short-title.md`, for example `2026-12-05-first-hydrogen-line.md`. The date in the name is the entry's date.
2. Start it with this header, then write the entry in Markdown below it:

   ```yaml
   ---
   title: "First hydrogen-line detection with an RTL-SDR"
   entry: 2
   summary: "One sentence that appears under the title and on the home page."
   target: "Short target line for the home page"
   log:
     - label: Target
       value: "Galactic plane at l = 30°"
     - label: Line
       value: "HI, 1420.4058 MHz (21 cm)"
     - label: Telescope
       value: "My dish and RTL-SDR Blog V3"
     - label: Result
       value: "..."
   downloads:
     - label: "Code"
       url: /projects/hydrogen-line/
   ---
   ```

   Every field except `title` is optional. `log` fills the observation card at the top of the entry, and `downloads` fills the "Files for this entry" list at the bottom.
3. Put figures in `assets/images/<entry-name>/` and add them with:

   ```html
   <figure>
     <img src="{{ '/assets/images/<entry-name>/plot.png' | relative_url }}" alt="Describe what the plot shows">
     <figcaption>What the reader should notice.</figcaption>
   </figure>
   ```

4. For a highlighted caveat, use the red-pencil note:

   ```html
   <div class="note-pencil"><strong>Not proven:</strong> ...</div>
   ```

5. Commit and push (or upload). The site rebuilds automatically.

The masthead trace is the W3(OH) spectrum from entry 1. To swap it for a later observation, replace the path in `_includes/trace.svg`.

## Notes
- Readers can subscribe to new entries through the RSS feed at `/feed.xml`.
