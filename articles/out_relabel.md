# relabel Output Plugin

The `relabel` output plugin re-labels events.


## Example Configuration

`out_relabel` is included in Fluentd's core. No additional installation
process is required.

``` {.CodeRay}
<match pattern>
  @type relabel
  @label @foo
</match>

<label @foo>
  <match pattern>
    ...
  </match>
</label>
```

The above example puts a label `@foo` to matched events, and the `label`
directive can take care of these events.

FYI: All of input and output plugins also have `@label` parameter
provided by Fluentd core. The `relabel` plugin is a plugin which
actually does nothing, but supports only `@label` parameter.


## Supported modes

-   Non-Buffered

-   See also: [Output Plugin Overview](/articles/output-plugin-overview.md)


## Plugin helpers

-   [event\_emitter](/articles/api-plugin-helper-event_emitter.md)


## Parameters

[Common Parameters](/articles/plugin-common-parameters.md)

[]{#@type}

### \@type

The value must be `relabel`.

[]{#@label}

### \@label

The label.

    type         default         version
  -------- -------------------- ---------
   string   required parameter   0.14.0


------------------------------------------------------------------------

If this article is incorrect or outdated, or omits critical information, please [let us know](https://github.com/fluent/fluentd-docs/issues?state=open).
[Fluentd](http://www.fluentd.org/) is a open source project under [Cloud Native Computing Foundation (CNCF)](https://cncf.io/). All components are available under the Apache 2 License.