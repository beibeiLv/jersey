<!--

    DO NOT ALTER OR REMOVE COPYRIGHT NOTICES OR THIS HEADER.

    Copyright (c) 2015-2017 Oracle and/or its affiliates. All rights reserved.

    The contents of this file are subject to the terms of either the GNU
    General Public License Version 2 only ("GPL") or the Common Development
    and Distribution License("CDDL") (collectively, the "License").  You
    may not use this file except in compliance with the License.  You can
    obtain a copy of the License at
    https://oss.oracle.com/licenses/CDDL+GPL-1.1
    or LICENSE.txt.  See the License for the specific
    language governing permissions and limitations under the License.

    When distributing the software, include this License Header Notice in each
    file and include the License file at LICENSE.txt.

    GPL Classpath Exception:
    Oracle designates this particular file as subject to the "Classpath"
    exception as provided by Oracle in the GPL Version 2 section of the License
    file that accompanied this code.

    Modifications:
    If applicable, add the following below the License Header, with the fields
    enclosed by brackets [] replaced by your own identifying information:
    "Portions Copyright [year] [name of copyright owner]"

    Contributor(s):
    If you wish your version of this file to be governed by only the CDDL or
    only the GPL Version 2, indicate your decision by adding "[Contributor]
    elects to include this software in this distribution under the [CDDL or GPL
    Version 2] license."  If you don't indicate a single choice of license, a
    recipient has the option to distribute your version of this file under
    either the CDDL, the GPL Version 2 or to extend the choice of license to
    its licensees as provided above.  However, if you add GPL Version 2 code
    and therefore, elected the GPL Version 2 license, then the option applies
    only if the new code is made subject to such option by the copyright
    holder.

-->

Entity Data Filtering
=====================

### *Role-based Entity Filtering using security annotations*

This example demonstrates how to use entity filtering feature together
with security annotations (from `javax.annotation.security` package) and
how to apply them on domain classes as well as on JAX-RS resource
classes or JAX-RS resource methods.

In addition to domain classes and JAX-RS resources (with security
annotations applied) there is also one (pre-matching) container request
filter, `SecurityRequestFilter`. The filter sets security context for
each incoming request as if the request was invoked by a user in role
"manager".

The full description how Entity Data Filtering can be found in Jersey
User Guide, chapter [Entity Data
Filtering](https://jersey.java.net/documentation/latest/entity-filtering.html).
Sections relevant to this example (describing this exact example) are:

-   [Enabling and configuring Entity Filtering in your
    application](https://jersey.java.net/documentation/latest/entity-filtering.html#d0e13911)
-   [Role-based Entity Filtering using (javax.annotation.security)
    annotations](https://jersey.java.net/documentation/latest/entity-filtering.html#ef.security.annotations)

Contents
--------

The mapping of the URI path space is presented in the following table:


URI path                                  | Resource class         | HTTP methods   | Notes
----------------------------------------- | ---------------------- | -------------- | ----------------------------------------------------------------------------------------------
**_/restricted-resource/denyAll_**        | RestrictedResource     | GET            | `@DenyAll` - returns HTTP 403, Forbidden response
**_/restricted-resource/permitAll_**      | RestrictedResource     | GET            | `@PermitAll` - Role-based view on RestrictedEntity class - permitAll, simpleField
**_/restricted-resource/rolesAllowed_**   | RestrictedResource     | GET            | `@RolesAllowed({"manager"})` - Role-based view on RestrictedEntity class - permitAll, simpleField, mixedField.managerField
**_/unrestricted-resource_**              | UnrestrictedResource   | GET            | No security annotation used, user in role "manager" - Role-based view on RestrictedEntity class - permitAll, simpleField, mixedField.managerField

Application is based on Grizzly container (see `App`). Everything needed
(resources/providers) is registered in
`SecurityEntityFilteringApplication`.

Running the Example
-------------------

Run the example as follows:

>     mvn clean package exec:java

This deploys current example using Grizzly. You can access the
application at:

-   <http://localhost:8080/unrestricted-resource>
-   <http://localhost:8080/restricted-resource/denyAll>
-   <http://localhost:8080/restricted-resource/permitAll>
-   <http://localhost:8080/restricted-resource/rolesAllowed>
-   <http://localhost:8080/restricted-resource/runtimeRolesAllowed?roles=manager,user>

Using Jackson instead of MOXy
-----------------------------

This examples uses by default Entity Data Filtering feature together
with MOXy. To switch MOXy JSON provider to Jackson (2.x) JSON provider
simply

-   comment registration of MOXy ContextResolver, and\
     `register(new MoxyJsonConfig().setFormattedOutput(true).resolver())`
-   uncomment registration of JacksonFeature\
     `register(JacksonFeature.class)`

in `SecurityEntityFilteringApplication` class.
