# Android Resource Translator

*WIP*

**Android Resource Translator** scans your `strings.xml` files for missing translations and automatically translates them using OpenAI’s language models.

---

## GitHub Actions Workflow Examples

### Simple Example

If you want to quickly try out the action with minimal configuration, add this step to your workflow:

```yaml
- name: Run Android Resource Translator
  uses: duartebarbosadev/AndroidResourceTranslator@v1
  env:
    OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}
```

### Advanced Workflow Example

For a more complete setup—including checking out your repository, running the translator with additional options, and automatically creating a pull request with the translation report—use the following workflow snippet:

```yaml
name: Auto-translate strings.xml

on:
  push:
    branches:
      - main

permissions:
  contents: write
  pull-requests: write

concurrency:
  group: "${{ github.workflow }}-${{ github.ref }}"
  cancel-in-progress: true

jobs:
  perform-auto-translate:
    runs-on: ubuntu-latest
    timeout-minutes: 15

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v4

      - name: Translate strings.xml to supported languages
        id: translate
        uses: duartebarbosadev/Android-Resource-Translator@v1
        #with:
          # (Optional inputs)
          #project_path: "./app/src/main/res"
          #openai_model: "gpt-3.5-turbo"
          #log_trace: "false"
        env:
          OPENAI_API_KEY: ${{ secrets.OPENAI_API_KEY }}

      - name: Create Pull Request
        uses: peter-evans/create-pull-request@v7
        with:
          branch: auto-translate  # Fixed branch name
          commit-message: "[Translate Bot] Auto-generated translations for non-English languages"
          committer: "github-actions[bot] <41898282+github-actions[bot]@users.noreply.github.com>"
          author: "${{ github.actor }} <${{ github.actor_id }}+${{ github.actor }}@users.noreply.github.com>"
          signoff: "false"
          title: "[Translate Bot] Auto-generated translations for non-English languages"
          body: |    
            ${{ steps.translate.outputs.translation_report }}
    
            This pull request was automatically generated.
          labels: "translation, automated pr"
          assignees: "[yourname]"
          reviewers: "[yourname]"
```

## Local Execution

To run the translator on your local machine, execute:

```bash
python AndroidResourceTranslator.py /path/to/your/android/project --auto-translate --openai-model gpt-3.5-turbo --ignore-folders build
```

You can also pass additional parameters like `--project-context` and `--log-trace` as needed.

---

## Configuration

The action supports the following inputs:

| Input                     | Description                                                                              | Default Value               | Optional |
| ------------------------- | ---------------------------------------------------------------------------------------- | --------------------------- | :------: |
| **project_path**    | Path to the Android project base directory.                                              | `${{ github.workspace }}` |   Yes   |
| **auto_translate**  | Automatically translate missing resources. Set to `false` to disable auto-translation. | `"true"`                  |   Yes   |
| **log_trace**       | Enable detailed logging. Use `"true"` for verbose output.                              | `"false"`                 |   Yes   |
| **openai_model**    | Specify the OpenAI model to use for translation. We advise using `gpt-3.5-turbo`.      | `"gpt-3.5-turbo"`         |   Yes   |
| **project_context** | Additional project context to include in translation prompts.                            | `""`                      |   Yes   |
| **ignore_folders**  | Comma-separated list of folder names to ignore during resource scanning (e.g., build).   | `"build"`                 |   Yes   |

---

## Translation Report Output

After the translation process is executed, the action produces an output called `translation_report`. This report is generated in Markdown format and includes a detailed summary of all translations performed. The report contains:

- **Module Information:**Each module found in your Android project is listed along with its language-specific resources.
- **Strings Translations:**For each module and language, a table displays every key that was translated, including the original text and the resulting translation.
- **Plural Resources Translations:**
  If plural resources were translated, the report shows each plural resource name along with a table listing each plural quantity (e.g., `one`, `few`, `many`) and their corresponding translations.

This output is particularly useful for manual review and validation. In the provided advanced workflow, the report is automatically inserted into the body of a pull request, allowing maintainers to inspect the changes before merging.

---

## Projects Using Android Resource Translator

We love to see our tool in action! Here are some projects that are using Android Resource Translator:

- **[Scrolless](https://github.com/duartebarbosadev/Scrolless/)**

*Have a project using Android Resource Translator? Let us know by submitting a pull request to add your project to this list!*

---

## Contributing

Contributions are very welcome! If you have ideas for improvements or spot any issues, please open an issue or submit a pull request.

---

## Changelog

All notable changes are documented in [CHANGELOG.md](./CHANGELOG.md).

---

## License

This project is licensed under the [MIT License](./LICENSE).

---

If you have any questions or need assistance, feel free to open an issue or reach out.

*Built with ❤️ by [Duarte Barbosa](https://github.com/duartebarbosadev).*

---
