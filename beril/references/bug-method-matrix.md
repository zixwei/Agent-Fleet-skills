# Bug method matrix

One line per test. Use it to choose the next check, not to run all of them. Each row names a signal, a minimal test, and the proof that closes it.

Full procedures are in `techniques.md`, `vulnerability-research.md`, `mobile-reverse.md`, and `firmware-iot.md`.

## Surface and recon

| Signal | Test | Proof |
|---|---|---|
| Scope with acquisitions | Certificate and ASN pivot | Ownership source for the asset |
| Old hostname in CT logs | Resolve and fingerprint | Live service with a version |
| JS bundle with routes | Extract paths and compare with the UI | Reachable hidden endpoint |
| Dangling CNAME | Claim the service | Controlled page on the hostname |
| `.git` or `.svn` exposed | Fetch metadata | Source or revision data |
| Directory listing | Read the index | File names and sizes |
| Debug header | Compare with a clean request | Backend or version leak |
| Staging host | Test the same endpoint | Weaker auth or debug output |
| Public bucket name | Read the ACL | Object list or file |
| Source map | Fetch the map | Original names and paths |

## Authentication and session

| Signal | Test | Proof |
|---|---|---|
| Login form | Username enumeration by response and timing | Valid vs invalid difference |
| Reset flow | Host header poisoning | Reset link to your host |
| Reset token | Reuse after use or after password change | Second reset works |
| Session cookie | Check flags and rotation | Missing HttpOnly or no rotation on login |
| Remember-me token | Decode and test expiry | Predictable or non-expiring token |
| JWT | Algorithm, `none`, key confusion | Forged token accepted |
| JWT claim | Change role or subject | Higher privilege page |
| OAuth redirect | Path, subdomain, open redirect | Code sent to your host |
| OAuth `state` | Remove or reuse | Login CSRF |
| OIDC `nonce` | Replay an ID token | Token accepted twice |
| SAML assertion | XML signature wrapping | Assertion for another user |
| SAML attribute | Pollution or namespace confusion | Privilege claim changes |
| MFA step | Skip the challenge route | Session without MFA |
| MFA code | Reuse or brute force | Same code twice |
| Password change | Session invalidation | Old session still valid |
| Account deletion | Token and session lifetime | Deleted account still acts |

## Access control

| Signal | Test | Proof |
|---|---|---|
| Object id in URL | Swap with a second account | Other account's object |
| UUID | Enumerate a sibling endpoint | Cross-account read |
| GraphQL node | Query by node id | Restricted object |
| Tenant header | Change tenant | Other tenant data |
| Role cookie | Set `role=admin` | Admin page |
| Hidden field | Add `role` or `isAdmin` | Role change |
| API version | Call the older version | Weaker auth |
| Export endpoint | Request another object | Full record |
| Batch endpoint | Mix ids from two users | Mixed response |
| File id | Guess a sequential id | Private file |

## Injection

| Signal | Test | Proof |
|---|---|---|
| SQL error | Quote and comment | Error or boolean change |
| Numeric id | `OR 1=1` then UNION | Extra row |
| Blind SQL | Time delay | Repeatable delay |
| NoSQL JSON | `$ne`, `$gt`, array coercion | Auth bypass or extra row |
| Template | `{{7*7}}` variants | `49` and command output |
| Command | `;id`, `$(id)`, backticks | `uid=` |
| LDAP | `*)(uid=*` | Extra entry |
| XPath | Quote and boolean | Node change |
| Header | CRLF in a header value | Injected header |
| Log | Newline and fake entry | Forged log line |
| CSV | Formula prefix | Formula in the export |
| PDF generator | HTML or file URL | Local file read |
| Email | HTML and CSS injection | Client execution |
| Deserialization | Language-specific gadget | Callback or command |
| XXE | External entity | File read or SSRF |
| SSTI sandbox | Engine fingerprint | File read or command |
| ORM injection | JSON operator | Extra row |
| GraphQL argument | Type confusion | Bypass or data leak |

## Client-side

| Signal | Test | Proof |
|---|---|---|
| Reflected value | HTML context probe | Script execution |
| DOM sink | `innerHTML`, `eval`, `postMessage` | Execution in the browser |
| Stored field | Profile or comment | Execution for another user |
| `postMessage` | Missing origin check | Cross-origin message action |
| CSP | JSONP, base, gadget | Script under policy |
| Sanitizer | Mutation XSS | Execution after sanitize |
| Markdown | Raw HTML or image URL | Execution or exfil |
| File upload preview | SVG or HTML | Execution on the origin |
| CSV export | Formula | Spreadsheet execution |
| WebSocket | Cross-origin handshake | Unauthenticated action |

## SSRF, files, and parsers

| Signal | Test | Proof |
|---|---|---|
| URL fetch | Collaborator callback | Hit from the server |
| Allowlist | Parser differential URL | Internal host reached |
| Redirect | 302 to an internal host | Internal response |
| DNS rebinding | Short TTL to internal IP | Second resolution internal |
| Metadata | Cloud metadata endpoint | Role or credentials |
| File upload | Extension, MIME, magic | Served file |
| Archive | `../` entry | File outside root |
| Filename | Traversal or null | Write outside root |
| Image parser | Malformed dimensions | Crash or memory bug |
| Zip bomb | Nested high ratio | Disk or memory exhaustion |

## Logic, race, and business

| Signal | Test | Proof |
|---|---|---|
| Price field | Change price or currency | Wrong invoice |
| Negative quantity | Negative or zero | Credit balance |
| Coupon | Apply twice or across accounts | Double discount |
| Refund | Refund before capture | Money out |
| Approval | Parallel approve and edit | Two states |
| Limit | Single-packet or parallel requests | Limit exceeded |
| Invite | Reuse token | Second join |
| Trial | Restart with a new identifier | Extended trial |
| Cart | Change quantity after checkout | Price mismatch |
| Currency | Rounding or decimal precision | Fractional gain |

## Cloud and Kubernetes

| Signal | Test | Proof |
|---|---|---|
| Metadata reachable | Read role name | Role from the instance |
| Public bucket | List or read | Object data |
| IAM wildcard | `sts get-caller-identity` | Effective permissions |
| Trust policy | Assume a role | Credentials for the role |
| Container token | Read the service account | API call as the pod |
| K8s API | List pods or secrets | Cluster data |
| Privileged pod | Mount host path | Host file read |
| Admission webhook | Bypass label | Pod without policy |
| CI token | Read the environment | Registry or cloud access |
| Registry | Pull a private image | Image layers |

## Mobile

| Signal | Test | Proof |
|---|---|---|
| Exported activity | `am start` | Activity with attacker data |
| Deep link | Arbitrary URL to WebView | Page with bridge access |
| JS bridge | Call a method from a page | Token or action |
| Backup enabled | `adb backup` | App data |
| Cleartext | Proxy the API | Token in plaintext |
| Pinning | Hook the verifier | Intercepted traffic |
| Provider | Request a broad path | File outside the intended dir |
| Hardcoded key | Use it against the API | Authorized call |

## AI and LLM

| Signal | Test | Proof |
|---|---|---|
| System prompt | Direct injection | Prompt and secret |
| RAG chunk | Indirect instruction | Tool call or exfil |
| Markdown | Image URL with data | Request to your host |
| Tool boundary | Attacker-chosen argument | File read or email |
| Cross-user | Shared vector store | Other user's chunk |
| Agent memory | Poisoned note | Later action |
| Output parser | JSON or XML break | Tool confusion |
| Context window | Long input truncation | Dropped guardrail |
| File upload | RAG poisoning | Retrieved instruction |
| Cost limit | Alias or batch query | Rate bypass |

## Binary and RE

| Signal | Test | Proof |
|---|---|---|
| Unsafe copy | Long input | Crash with controlled offset |
| Format string | `%p` input | Stack leak |
| UAF | Free then reallocate | Stale callback |
| Integer math | Wrapping size | Small allocation |
| Missing bounds | Length field | OOB read or write |
| Command injection | Config or request to `system` | Command output |
| Parser differential | Two parsing layers | Different object |
| Update path | Unsigned image | Modified rootfs |
| SUID binary | `checksec` and strings | Privilege path |
| Hardcoded secret | `strings` | Live credential |

## Triage and report

| Check | Question |
|---|---|
| Reachability | Can an attacker do it now? |
| Boundary | Which trust boundary is crossed? |
| Impact | Data, money, account, code? |
| Reproducibility | Does it work from a clean state? |
| Duplicate | Is it already known? |
| Evidence | Is the request or input minimal? |
| Fix | What is the smallest correct change? |
