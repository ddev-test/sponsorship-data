# DDEV Machine-Readable (JSON) Sponsorship Data

Current information about DDEV's sponsors and current needs.

The data here is accumulated from a few sources:

* `all-sponsorships.json` is the summary of all DDEV sponsors, deployed via GitHub Pages at:
  - **https://ddev.github.io/sponsorship-data/data/all-sponsorships.json** (API endpoint)
* GitHub Sponsors data for the `ddev` organization is updated daily and automatically by the `github-sponsorships.sh` script here.
* GitHub Sponsors data for the `rfay` user (goes into the same DDEV Foundation bank account) is updated daily and automatically by the `github-sponsorships.sh` script here. (A few sponsors started way back when we didn't have the `ddev` org and have never switched over.)
* The `invoiced-sponsorships.jsonc` and `paypal-sponsorships.jsonc` are manually maintained here when generous donors sign up for these avenues.

## API Usage

The sponsorship data is available as a [JSON file](https://ddev.com/s/sponsorship-data.json), which redirects to this repository's GitHub Pages at [https://ddev.github.io/sponsorship-data/data/all-sponsorships.json](https://ddev.github.io/sponsorship-data/data/all-sponsorships.json)

## Tools and Scripts

### Sponsorship History

To provide sponsorship history (previously available via git history of `all-sponsorships.json`), each daily deployment now saves a snapshot of the data in `data/history/YYYY-MM-DD.json`.  
**These snapshots are committed to the `history` branch of this repository.**  
You can analyze sponsorship changes over time by checking out the `history` branch:

```bash
git fetch origin history
git checkout history
# Now data/history/ contains all snapshots
```

#### Example: Show total monthly average income over time

```bash
for f in data/history/*.json; do
  date=$(basename $f .json)
  value=$(jq '.["total_monthly_average_income"]' < "$f")
  echo "$date $value"
done | sort
```

### Sponsorship progress for the past two months

`git log --since="2 months ago" --format="%H %ad" --date=short --reverse -- data/all-sponsorships.json | while read commit date; do   value=$(git show $commit:data/all-sponsorships.json | jq '.["total_monthly_average_income"]');   echo "$date $value"; done`

_Note: For recent history, use the new `data/history/` snapshots in the `history` branch as described above._

## DDEV Foundation

To learn more about the DDEV Foundation and its funding, see [DDEV Foundation](https://ddev.com/foundation).

## DDEV Funding

See these resources:

* [Support DDEV](https://ddev.com/support-ddev/)
* [GitHub Sponsors for DDEV](https://github.com/sponsors/ddev)

## How to Manually Test This PR (History Branch Workflow)

Because this PR is on the main `ddev/sponsorship-data` repository and GitHub branch/environment protection rules prevent PR branches from deploying to GitHub Pages or pushing to protected branches, you cannot fully test the workflow end-to-end from a PR branch in this repository.

**However, you can verify the workflow logic and steps as follows:**

1. **Review the Workflow Steps**

   - Confirm that the workflow includes steps to:
     - Generate the sponsorship data.
     - Save a dated snapshot to `data/history/YYYY-MM-DD.json`.
     - Commit and push the snapshot to the `history` branch.
     - Deploy the latest data to GitHub Pages.

2. **Test Locally (Optional)**

   - You can run the relevant scripts locally to ensure they generate the expected files:
     ```bash
     scripts/combine-sponsorships.sh
     mkdir -p data/history
     cp data/all-sponsorships.json data/history/$(date +%F).json
     ```
   - Check that `data/history/YYYY-MM-DD.json` is created and valid.

3. **Test in a Fork (Optional, for Full End-to-End)**

   - If you want to test the full workflow (including branch pushes and Pages deployment), fork the repository and follow these steps:
     - Push your PR branch to your fork.
     - Optionally, edit `.github/workflows/deploy-api.yml` in your fork to allow the workflow to run on your branch.
     - Push a commit to trigger the workflow.
     - Verify that the workflow runs, updates the `history` branch, and deploys to GitHub Pages in your fork.

**Note:**  
Due to GitHub security restrictions, PR branches in the main repo cannot push to protected branches or deploy to protected environments. Full end-to-end testing is only possible in a fork or after merging to `main`.
