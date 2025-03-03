We started this assignment with three targets. One Linux, two Windows.

All three have now been fully compromised -- well done!

Hopefully you've been taking notes and are now about to start writing a report on the topic. If you're not familiar with pentest reports, the following task may come in handy. Additionally, Offensive Security have also published an example penetration test report [here](https://www.offensive-security.com/reports/penetration-testing-sample-report-2013.pdf), and there is a whole community-curated repository of public reports [here](https://github.com/juliocesarfort/public-pentesting-reports) should you need more inspiration.  

---

Penetration test reports are generally split into several sections. There is no strictly defined standard unfortunately, but the following layout should be well received:  

- First up is the **_Executive Summary_**. This should be essentially non-technical, providing a brief overview of the job that was contracted to (and completed by) the pentester, including a concise summary of the scope of the engagement. You should also include a very short summary of the results here, as well as a concise analysis of the overall security posture of the company. Be aware thought that, as the name suggests, this section is designed to be read by the higher-ups in a company who may not have a technical background or the time to devote to a long-winded explanation. This section is particularly important as in many cases it may be the only section that the client actually looks at. It should catch the eye, and will set the tone for the rest of the report.  
    
- At the end of (or immediately after) the executive summary include a _**Timeline**_ showing an overview of what you did and when you did it. This allows whoever is assigned to fix the vulnerabilities to check any logs from the compromised system and see what a successful attack looks like from their own privileged perspective.  
    
- Next we have the _**Findings and Remediations**_ section. This should be a more technical section. It should provide a detailed explanation of thevulnerabilities you found _as well as your suggested fixes for these._ Additionally, you should indicate the severity of each vulnerability, and the risk to the company should the vulnerability be exploited by a bad actor -- the [CVSS calculator](https://www.first.org/cvss/calculator/3.1) will be useful for this. You should not necessarily be providing a step-by-step account of your methodology here, but there should be enough detail for a technically-able person to see what the problem is, and what the solutions might be.  
    
- After the findings and remediations should come the _**Attack Narrative**_. This _should_ be a step-by-step writeup of the actions you took against the targets, including enough detail for a technically-competent individual to replicate the attacks exactly in an almost copy-and-paste approach. In many ways this is similar to a detailed write-up for a CTF.
- A section that is good to include but often skipped: the _**Cleanup**_ section. This should detail the actions you took to eradicate your presence on the targets (e.g. removing any added accounts, deleting exploits or created files, etc).
- Next (but not last), there should be a _**Conclusion**_. This just summarises the report, rounding off the results and stressing the importance of patching as required.
- Finally you should include _**References**_ then _**Appendices**_. The references section includes full references to any works cited throughout the report (for example, maybe a quote or table from the OWASP website, or referencing a newspaper article on an attack which utilised a vulnerability found in the target network). The references section should also be used to link to relevant CVEs (Common Vulnerability and Exposure), CWEs (Common Weakness Enumerations), and/or CAPECs (Common Attack Pattern Enumerations and Classifications) for the found vulnerabilities. Your appendices should include any large pieces of information that would have cluttered up the main text. For example, if you had to edit an exploit (as we did during the Wreath network), you should include a full copy of the edited code as an appendix and reference it when mentioned in the other sections. Equally, any code you write should also be stored here (with the exception of short snippets and one-liners, which can be placed inline at the relevant section), along with any large amounts of data or big tables / diagrams.  
    

So, the sections should be:

1. Executive Summary
2. Timeline
3. Findings and Remediations
4. Attack Narrative
5. Cleanup
6. Conclusion
7. References
8. Appendices  


Pentest reports will usually also have a branded front-cover and a table of contents before the report itself begins.  

There are many pentest report templates available on the Internet which can be used to provide a baseline for this. Many companies will also provide their penetration testers with a company-specific template to follow. Regardless, of whether you use a pre-built template or create your own, find a style and stick with it!

---

_With your report written and proof-read, you send the PDF to Thomas then sit back and relax, your work is done!_

**Next step:** [[Final Thoughts]]
