# Communities and AI

The AI policies of the projects behind Linux phones, and how to report a bug to them well.

Policies change. Re-read the linked page before you post; the dates below are when these were checked, not a guarantee of what they say now.

## postmarketOS / Nura: no generative AI

postmarketOS was renamed **Nura** in 2026. Their AI policy (<https://docs.nura.eco/policies-and-processes/development/ai-policy.html>, in force since 2026-02) does not allow generative-AI contributions or promotion. As recorded, it forbids:

- "Submitting contributions fully or in part created by generative AI tools to Nura"
- "Recommending generative AI tools to other community members"

Their Code of Conduct lists "using or promoting generative AI tools" as unacceptable behaviour, and the policy covers issues, GitLab and their chat rooms, with escalation up to bans.

**What this means for you:** do not have an assistant draft issues, merge requests or messages for postmarketOS/Nura. If you need to report something there, **you write it yourself**, in your own words. An assistant can help you understand the problem and gather evidence; it does not write the post.

## sm7125-mainline: no AI policy

The **sm7125-mainline** project (the kernel and firmware work behind this family, on GitHub) has **no AI policy**. Its `linux` and `firmware-xiaomi-miatoll` repositories accept issues; its `u-boot` repository does not take issues. Because there is no rule against it, an AI-assisted report is fine there: but say so. An honest note that AI helped you put the report together is good practice and costs nothing.

## Linux kernel: AI assistance allowed, with disclosure

The Linux kernel allows AI assistance if you disclose it. As recorded:

- Disclose AI assistance in the **changelog / cover letter**.
- A **human verifies** the change and **sends** it.
- **No AI `Signed-off-by`.**

The references are <https://docs.kernel.org/process/generated-content.html> and <https://docs.kernel.org/process/coding-assistants.html>.

## ModemManager / libqmi: no policy found

No AI policy was found for **ModemManager** or **libqmi** when these were checked. Treat that as "none known", not "explicitly allowed", and be transparent anyway.

## How to report a bug well

Whatever the community's AI policy, a good report contains evidence, not guesses, and a person stands behind every line of it:

1. **State the device and software precisely.** Codename, kernel version, package versions, and the distribution release. "Redmi Note 9 Pro Max (`excalibur`), postmarketOS v26.06, kernel 6.14.7" beats "my phone".
2. **Say what you expected and what happened.** One sentence each.
3. **Include the exact commands and their output.** Trim to the relevant lines; say what you trimmed.
4. **Include what you already ruled out.** If you stopped ModemManager, restarted the remoteproc, and it still failed, say so: it saves maintainers asking and shows the report is grounded. See the narrowing steps in [`qualcomm-modem-debugging.md`](qualcomm-modem-debugging.md) as a model.
5. **Keep it reproducible.** A minimal sequence someone else can follow.
6. **Follow the project's channel and policy.** Issues in the repo that takes them, not the one that doesn't; and for postmarketOS/Nura, written by you.

A report that shows the work (what you tried, what you ruled out, what the logs say) is welcome in every community. Doing that work is also where an assistant is most useful, before you write the report yourself.

## Last verified

Policies as recorded and checked 2026-11-20; this guide checked 2026-11-21.
