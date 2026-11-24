# Method cards

Each card is a transferable procedure, not an article summary. Use it when the signal matches. Change the payloads, keep the reasoning.

The cards assume written authorization for the target. A lab or a bounty program with the asset in scope counts. Nothing here is a license to test a stranger's system.

## How to read a card

- **When** names the situation that makes the card relevant.
- **Signal** is what you observe before spending time.
- **Method** is the shortest path to a yes or no.
- **Proof** is the evidence a triager or client will accept.
- **Pitfalls** lists the false positives that waste a report.

## Recon and surface

### 1. Build the asset graph first

**When:** A program gives you a domain and a wide scope, and you do not know where the application ends.

**Signal:** The scope mentions acquisitions, mobile apps, APIs, or "all assets owned by the company".

**Method:**

1. List the root domains in scope.
2. Pull certificates from crt.sh and Censys for each domain.
3. Resolve the organization's ASNs and cloud ranges.
4. Add mobile apps, status pages, docs, and third-party integrations.
5. Mark each asset with owner, environment, and last change date if you can find it.
6. Rank by business value and by how rarely it appears in public writeups.

**Proof:** A graph or table that shows why each asset belongs to the target, with a source for the ownership claim.

**Pitfalls:** Shared hosting and CDN IPs belong to many customers. An IP in an ASN is not automatically in scope.

**Sources:** Jason Haddix methodology talks, ProjectDiscovery recon guides, program scope pages.

### 2. Subdomain takeover

**When:** The target uses a third-party service for docs, status, marketing, or support.

**Signal:** A CNAME points to a service that no longer has a matching resource.

**Method:**

1. Collect CNAMEs and resolve them.
2. Compare the response body and status against the service's "unclaimed" page.
3. Check the service's takeover rules. Some services require a specific 404 body, not a dangling CNAME alone.
4. Claim only if the program allows it and the service is free. Use a harmless page.
5. Prove control with a unique string in the response.

**Proof:** The claimed page served from the target's hostname, plus the CNAME chain.

**Pitfalls:** A dangling CNAME to a parked domain is not a takeover. A service that requires a paid plan may not be claimable within the engagement.

**Sources:** `can-i-take-over-xyz`, HackerOne disclosed takeover reports.

### 3. JavaScript and source map mining

**When:** The application is a single-page app or a mobile API client.

**Signal:** Large bundles, source maps enabled, framework chunks with route names.

**Method:**

1. Download the bundles and source maps.
2. Extract strings that look like paths, GraphQL operations, feature flags, and internal hosts.
3. Look for commented-out endpoints and old API versions.
4. Compare the current bundle with an older one from the Wayback Machine.
5. Test the endpoints you find with a low-privilege account.

**Proof:** A request to an endpoint that is not linked in the UI, with a response that shows the behavior.

**Pitfalls:** A string in a bundle may be dead code. A source map may expose TypeScript but no secrets. Check reachability before reporting.

**Sources:** `unfurl`, `LinkFinder`, `sourcemapper`, ProjectDiscovery blog.

### 4. Parameter mining

**When:** A endpoint behaves differently with a parameter you have not seen.

**Signal:** JSON responses with fields that the UI does not send, or a form with hidden inputs.

**Method:**

1. Collect URLs from crawling, Wayback, and the JS bundles.
2. Extract keys with `unfurl -u keys`.
3. Test a short list of high-value names: `debug`, `admin`, `role`, `internal`, `redirect`, `url`, `file`, `callback`.
4. Compare responses with and without the parameter.
5. Send the parameter in JSON, form, and query form. Parsers differ.

**Proof:** A response diff that changes authorization, data, or a redirect target.

**Pitfalls:** A reflected parameter is not a finding. A size change can come from a random token.

**Sources:** Arjun, ParamSpider, `unfurl`.

### 5. GitHub and CI secret hunting

**When:** The target has public repositories or open-source components.

**Signal:** Repos with CI workflows, `.env` examples, deployment scripts, or old commits.

**Method:**

1. Search the org for `api_key`, `token`, `secret`, `password`, and cloud provider names.
2. Review commit history for deleted secrets. A secret removed in a later commit is still live.
3. Check GitHub Actions logs and artifacts for masked values that leaked.
4. Verify the secret with a read-only call before reporting.
5. Identify the scope: what does the key access, and what is the blast radius?

**Proof:** A read-only API response that shows the key works, plus the commit or log URL.

**Pitfalls:** A public key is not a secret. A key for a staging service may be worthless. `--only-verified` exists for a reason.

**Sources:** trufflehog, gitleaks, GitHub secret scanning docs.

## Web exploitation

### 6. Request smuggling and desync

**When:** A CDN, load balancer, or reverse proxy sits in front of an application server.

**Signal:** Different HTTP parsers in the chain, HTTP/1.1 and HTTP/2 mixed, or a proxy that rewrites headers.

**Method:**

1. Send a request with both `Content-Length` and `Transfer-Encoding`.
2. Try CL.TE, TE.CL, and TE.TE variants.
3. Use a timing delay to detect a stuck request.
4. Confirm with a request that poisons the next response or captures the next request.
5. For HTTP/2, test downgrade behavior and pseudo-header handling.

**Proof:** A second, clean client receives a response it did not request, or a timing signal repeats reliably.

**Pitfalls:** A single 400 does not prove desync. Shared proxies can make results non-deterministic. Test at low volume.

**Sources:** James Kettle, "HTTP Desync Attacks"; Tom Stacey, "CRLF-Powered Desync Attacks".

### 7. Cache poisoning and deception

**When:** A CDN or reverse proxy caches responses, especially with a permissive cache key.

**Signal:** Unkeyed headers, query parameters, or cookies change the response but not the cache key.

**Method:**

1. Identify the cache key: host, path, query, selected headers, cookies.
2. Find an input that changes the response but is not in the key.
3. Poison the cache with a distinctive value.
4. Request the page from a clean session and confirm the value.
5. Check whether the poison reaches other users or only your session.

**Proof:** A clean request returns the attacker-controlled value, with cache headers that show the hit.

**Pitfalls:** A cache hit for your own cookie is not global. `Vary` and cache normalization can invalidate the result. Do not poison a production page with a harmful payload.

**Sources:** James Kettle, "Web Cache Entanglement"; PortSwigger cache poisoning labs.

### 8. Host header and password reset poisoning

**When:** The app builds absolute URLs from the request, especially in emails.

**Signal:** Password reset, invite, or verification emails contain a link with the request host.

**Method:**

1. Send a password reset with a modified `Host`, `X-Forwarded-Host`, or `Forwarded` header.
2. Use a domain you control and check the email.
3. Confirm the token or link points to your host.
4. Check whether the token is valid and whether it leaks to your server.

**Proof:** An email or response containing your host, and a token that works.

**Pitfalls:** Many proxies normalize the host. A reflected host in the page is not the same as a host used in an email.

**Sources:** PortSwigger host header labs, HackerOne disclosed reports.

### 9. XSS through parser differentials

**When:** A sanitizer, templating engine, or WAF filters HTML.

**Signal:** The input is transformed, truncated, or escaped in a way that differs from browser parsing.

**Method:**

1. Identify every parser: sanitizer, template engine, browser, DOM sink.
2. Find a construct that is inert to the filter and active in the browser. Mutation XSS, namespace confusion, comment tricks, attribute boundaries.
3. Keep the payload short and avoid characters the filter normalizes.
4. Test in the target browser, including the framework's re-render.
5. For CSP, look for JSONP, Angular, base tags, and script gadgets.

**Proof:** Script execution in the target origin, with the exact input and the browser version.

**Pitfalls:** A payload that fires in an innerHTML test may not fire in the real page. A CSP report without execution is not a finding.

**Sources:** Gareth Heyes, "XSS in innerHTML without equals"; PortSwigger XSS labs; `PayloadsAllTheThings`.

### 10. SQL injection beyond the quote

**When:** A parameter reaches a database query, including JSON and headers.

**Signal:** Error messages, time delays, or a response that changes with boolean input.

**Method:**

1. Establish a true and false baseline.
2. Test quote, comment, and parenthesis behavior for the likely DBMS.
3. Move to time-based blind only when union and error are blocked.
4. Use out-of-band callbacks when the response is fully asynchronous.
5. Prove data access with a bounded query, then stop.

**Proof:** A reproducible extraction of a value the tester should not see, with the request and response.

**Pitfalls:** A WAF block page can look like a false condition. Time delays can come from network jitter. Do not dump the whole database.

**Sources:** `sqlmap` docs, OWASP WSTG, PortSwigger SQLi labs.

### 11. SSTI to RCE

**When:** User input is rendered by a template engine.

**Signal:** `{{7*7}}`, `${7*7}`, or `<%= 7*7 %>` appears as `49`.

**Method:**

1. Fingerprint the engine with a decision tree of expressions.
2. Confirm that code runs, rather than a value being evaluated.
3. Find a gadget for the engine and version.
4. Escalate from data access to command execution only if the program allows it.
5. Keep the payload minimal and record the exact version.

**Proof:** A computed value and a command output or callback from the target.

**Pitfalls:** Client-side template injection is not server-side RCE. Sandboxes differ by version.

**Sources:** PortSwigger SSTI labs, `PayloadsAllTheThings`.

### 12. SSRF and URL parser confusion

**When:** The server fetches a URL supplied by the user: webhooks, previews, imports, PDF rendering.

**Signal:** A URL parameter, a callback, or a fetch error that reveals internal behavior.

**Method:**

1. Use a collaborator or your own listener to confirm the server makes the request.
2. Map the validation: allowlist, denylist, regex, DNS resolution order.
3. Test parser differentials: `http:///host`, backslashes, userinfo, encoded delimiters, IPv4-mapped IPv6, decimal IPs.
4. Test redirects and DNS rebinding when the first hop is validated.
5. Target metadata endpoints and internal services only within scope.

**Proof:** A callback from the target, or a response body from an internal service that is not otherwise reachable.

**Pitfalls:** DNS-only SSRF is weak without data exfiltration. A 200 from your listener does not prove the response reaches the attacker.

**Sources:** Orange Tsai, "A New Era of SSRF"; HackerOne report 3923212 on curl URL parsing; `PayloadsAllTheThings`.

### 13. File upload, path traversal, and archive slip

**When:** The app accepts files, extracts archives, or serves user paths.

**Signal:** Upload endpoints, import features, ZIP or tar handling, download endpoints with a filename parameter.

**Method:**

1. Test the extension, MIME type, magic bytes, and content separately.
2. Check whether the file is served from a web-accessible path.
3. Test filename traversal and null bytes.
4. For archives, create entries with `../` and absolute paths and see where they land.
5. Chain upload to execution only if the server runs the file.

**Proof:** A file written outside the intended directory, or code execution from the uploaded file.

**Pitfalls:** A filename reflected in the response is not traversal. A ZIP slip needs the extraction path and the resulting file location.

**Sources:** 0x00sec ZIP parser research, PortSwigger file upload labs, `PayloadsAllTheThings`.

### 14. Race conditions and state machines

**When:** A limit, balance, invite, coupon, or approval can be used once.

**Signal:** A check followed by a write, with no transaction or lock.

**Method:**

1. Identify the state transition and the invariant.
2. Send the same request many times in a tight window. Use a single-packet attack when the server supports HTTP/2.
3. Compare the number of successes with the number of intended uses.
4. Test across accounts, not only within one session.
5. Stop at the smallest proof that breaks the invariant.

**Proof:** Two successes where one was allowed, with timestamps and request IDs.

**Pitfalls:** Network retries can look like a race. Server-side deduplication may hide the issue on a later request.

**Sources:** James Kettle, "Smashing the state machine"; PortSwigger race condition labs.

### 15. Business logic and price manipulation

**When:** The app has carts, coupons, refunds, plans, or approvals.

**Signal:** Client-supplied prices, quantities, roles, or status fields.

**Method:**

1. Map the intended flow and the fields the client controls.
2. Change one field at a time: price, currency, quantity, negative values, decimal precision.
3. Test the order of operations: apply coupon after tax, refund before capture, upgrade before payment.
4. Test cross-account effects: does a coupon apply to another user's cart?
5. Prove financial or access impact, not an odd response alone.

**Proof:** An order, invoice, or balance that differs from the intended amount, with the request sequence.

**Pitfalls:** A validation message is not a bypass. A discount that the business already allows is not a bug.

**Sources:** HackerOne disclosed logic reports, PortSwigger business logic labs.

### 16. IDOR and BOLA

**When:** Objects are fetched by an identifier and the endpoint checks authentication but not ownership.

**Signal:** Sequential IDs, UUIDs in URLs, GraphQL node IDs, tenant headers.

**Method:**

1. Create two accounts at the same privilege level.
2. Capture object IDs for account A.
3. Request them with account B's session.
4. Test read, update, delete, and export separately. Permission checks are often inconsistent.
5. Test sibling endpoints and older API versions.
6. For GraphQL, test node lookups and nested fields.

**Proof:** Account B receives or changes account A's object, with both sessions and the object IDs.

**Pitfalls:** A public object is not an IDOR. A 200 with an empty body may be a soft denial. Check the data, not the status code.

**Sources:** OWASP API Security Top 10, HackerOne IDOR reports, PortSwigger access control labs.

### 17. Authentication: OAuth, OIDC, SAML, JWT

**When:** The app delegates login or uses tokens across services.

**Signal:** Redirect URIs, `state`, `nonce`, JWKS endpoints, SAML assertions, cookie prefixes.

**Method:**

1. Map the flow and every place a token or assertion is accepted.
2. Test redirect URI matching: path traversal, subdomain, open redirect, fragment tricks.
3. Test `state` and `nonce` reuse, missing binding, and mix-up attacks.
4. For JWT, test algorithm confusion, `none`, key confusion, and claim trust. Verify the signature yourself.
5. For SAML, test signature wrapping and attribute pollution.
6. Confirm with a fresh browser profile and the victim account.

**Proof:** A session or token for the victim account, obtained through the flawed flow.

**Pitfalls:** A decoded JWT is not a bypass. A login CSRF needs a victim action, not a crafted link alone.

**Sources:** PortSwigger OAuth and SAML labs, Zakhar Fedotkin SAML research, `jwt_tool`.

### 18. GraphQL

**When:** The API exposes a GraphQL endpoint.

**Signal:** `/graphql`, `__schema`, a GraphiQL page, or a mobile client with operations.

**Method:**

1. Try introspection. If disabled, use field suggestions and a wordlist.
2. Test authorization per field and per mutation, not only per query.
3. Test batching, aliases, and nested queries for cost and rate-limit bypass.
4. Test node lookups for IDOR.
5. Test input coercion, null handling, and duplicate keys.

**Proof:** A restricted field or mutation returns data for a lower-privilege account.

**Pitfalls:** Introspection enabled is not a vulnerability by itself. A 200 with GraphQL errors is not success.

**Sources:** `graphql-cop`, `clairvoyance`, PortSwigger GraphQL labs.

## Infrastructure

### 19. Cloud metadata and IAM

**When:** You have SSRF, a foothold, or a cloud-hosted app.

**Signal:** A metadata endpoint is reachable, or the app uses an instance role.

**Method:**

1. Query the metadata service for the provider.
2. Retrieve temporary credentials and the attached role.
3. Call `sts get-caller-identity` and enumerate the policy.
4. Test the permissions you actually have, read-only first.
5. Look for a path to a more privileged role or a trust policy mistake.

**Proof:** A read-only API call that returns data the role should not access, or a documented privilege escalation path.

**Pitfalls:** Metadata access from a proxy may be blocked by IMDSv2. A role name is not a permission.

**Sources:** HackTricks cloud sections, AWS IAM documentation, HackerOne SSRF reports.

### 20. Active Directory paths

**When:** You have a domain account or a network foothold.

**Signal:** Kerberos, LDAP, SMB, or ADCS exposed.

**Method:**

1. Collect with BloodHound.
2. Check Kerberoastable and AS-REP-roastable accounts.
3. Check ACL edges: GenericAll, WriteDACL, ForceChangePassword.
4. Check ADCS templates with Certipy.
5. Find the shortest path to the agreed objective, then prove one edge.

**Proof:** A ticket, a password reset, or a certificate that grants the next access, with the path in BloodHound.

**Pitfalls:** A roastable account with a strong password is not a finding until you crack it. A DA path that requires domain admin to start is circular.

**Sources:** BloodHound docs, Certipy docs, `impacket`, HackTricks AD.

### 21. TLS certificate validation

**When:** A client library or appliance validates certificates, especially with custom CAs or name constraints.

**Signal:** Recent reports about OpenSSL, wolfSSL, MySQL Connector, or native trust stores.

**Method:**

1. Build a test CA and a chain with the property the library may mishandle: name constraints, key usage, SAN, wildcard, cross-signing.
2. Present the chain to the client.
3. Observe whether the connection succeeds when it should fail.
4. Test the same chain against a reference client.
5. Record library versions and the exact certificate fields.

**Proof:** A successful connection with a certificate the client should reject, plus a reference failure.

**Pitfalls:** A browser warning is not a bypass. A test with a self-signed cert proves nothing if the client is configured to trust it.

**Sources:** Recent HackerOne reports from stze and mschorm, OpenSSL advisories.

## Mobile, binary, AI

### 22. Mobile app surface

**When:** The program includes an Android or iOS app.

**Signal:** An APK or IPA is in scope, or the mobile API is separate from the web API.

**Method:**

1. Decompile and read the manifest or Info.plist.
2. List exported components, deep links, and permissions.
3. Grep for keys, endpoints, and feature flags.
4. Bypass pinning in a lab and inspect traffic.
5. Test the API directly with the mobile client's headers and tokens.

**Proof:** A request from the mobile API that the web client cannot make, or a component that another app can invoke.

**Pitfalls:** A hardcoded key may be a public client ID. Pinning bypass on a rooted device does not prove a server-side issue.

**Sources:** MobSF, Frida, objection, OWASP MASTG.

### 23. Binary exploitation: leak, then control

**When:** A native binary parses input and has a memory bug.

**Signal:** Unsafe string functions, fixed-size buffers, a crash on long input.

**Method:**

1. Run `checksec` and record the mitigations.
2. Find the offset with a cyclic pattern.
3. If PIE or ASLR is on, find an information leak: format string, uninitialized memory, partial overwrite.
4. Compute the base and the gadget addresses.
5. Build the ROP chain. For a remote service, account for the socket and any null-byte limits.
6. Test locally with the target libc, then remotely.

**Proof:** A controlled instruction pointer or a shell, with the exploit script and the target version.

**Pitfalls:** A local shell does not prove remote exploitation. libc differences break offsets. A crash is not control.

**Sources:** 0x00sec PIE and ret2plt series, `pwntools` docs, ROPgadget.

### 24. LLM and agent features

**When:** The app has a chatbot, a RAG pipeline, or an agent with tools.

**Signal:** User text reaches a model prompt, and the model can call tools or read private data.

**Method:**

1. Map the trust boundary: system prompt, retrieved documents, user input, tool output.
2. Test direct injection, then indirect injection through a document, web page, or email.
3. Test whether the model can be made to call a tool with attacker-chosen arguments.
4. Test data exfiltration through markdown images, links, or tool calls.
5. Test cross-user leakage in shared chats and vector stores.
6. Prove impact with data the attacker should not see or an action they should not trigger.

**Proof:** A model output or tool call that crosses the boundary, reproducible with the same prompt.

**Pitfalls:** A prompt that says "ignore previous instructions" is not impact. A model that repeats the system prompt may be expected behavior. Check the threat model.

**Sources:** PortSwigger AI research, ProjectDiscovery Neo benchmark posts, OWASP LLM Top 10.

## Reporting

### 25. Build the impact chain

**When:** You have a valid bug and need a report that survives triage.

**Signal:** The bug works, but the impact feels small or theoretical.

**Method:**

1. Write the attacker's starting position: no account, one account, or a specific role.
2. Write the victim's position and what they must do, if anything.
3. Write the sequence of requests.
4. Name the harm: data read, data changed, money moved, account taken over, code executed.
5. Show the smallest proof, then the chain that makes it matter.
6. Separate what you tested from what you infer.

**Proof:** A reproduction that a triager can run in under five minutes, plus a short video or request log.

**Pitfalls:** A chain with an unproven step is a theory. CVSS without business context can undersell or oversell the finding.

**Sources:** Bugcrowd VRT, HackerOne report guidelines, the humanizer rules in the main skill.
