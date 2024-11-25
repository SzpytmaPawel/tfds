@using System.Globalization

<div class="settings-section">
    <div class="settings-card-title">
        <h7>Local agents config file</h7>
    </div>
    <div class="settings-card-content">
        <table class="table">
            <thead>
                <tr>
                    <th>Agent Value</th>
                    <th class="action-section">Action</th>
                </tr>
            </thead>
            <tbody>
                @foreach (var agent in editAgentValues.Select((value, index) => new { value, index }))
                {
                    <tr>
                        <td>
                            <SfTextBox @bind-Value="editAgentValues[agent.index]"
                                       Enabled="editIndex == agent.index" />
                        </td>
                        <td class="action-section">
                            @if (editIndex == agent.index)
                            {
                                <SfButton IconCss="bi bi-save-fill" OnClick="() => SaveAgent(agent.index)"></SfButton>
                            }
                            else
                            {
                                <SfButton IconCss="bi bi-pencil-fill" OnClick="() => EnableEditing(agent.index)"></SfButton>
                            }
                            <SfButton IconCss="bi bi-trash3-fill" OnClick="() => RemoveAgent(agent.index)"></SfButton>
                        </td>
                    </tr>
                }
                <tr>
                    <td>
                        <SfTextBox @bind-Value="newAgent" Enabled="true" />
                    </td>
                    <td class="action-section">
                        <SfButton IconCss="bi bi-plus-circle-fill" OnClick="AddAgent"></SfButton>
                    </td>
                </tr>
            </tbody>
        </table>
        <SfButton OnClick="SaveAllChanges" Content="Save changes"></SfButton>
        @if (!string.IsNullOrEmpty(validationMessage))
        {
            <p style="color: red;">@validationMessage</p>
        }
    </div>
</div>

@code {
    public string newAgent { get; set; } = string.Empty;
    public int? editIndex { get; set; } = null;
    public List<string> Agents { get; set; } = new();
    public List<string> editAgentValues { get; set; } = new();
    public string validationMessage { get; set; } = string.Empty;

    protected override void OnInitialized()
    {
        Agents.AddRange(new[] { "BLUEDK29", "BLUEFI29", "BLUENO29", "BLUESE29" });
        editAgentValues = new List<string>(Agents);
    }

    public void EnableEditing(int index)
    {
        editIndex = index;
    }

    public void SaveAgent(int index)
    {
        if (index >= 0 && index < editAgentValues.Count)
        {
            editAgentValues[index] = editAgentValues[index].Trim();
            editIndex = null;
        }
        StateHasChanged();
    }

    public void AddAgent()
    {
        if (string.IsNullOrWhiteSpace(newAgent))
        {
            validationMessage = "Agent value cannot be empty.";
        }
        else if (!System.Text.RegularExpressions.Regex.IsMatch(newAgent, "^[A-Z]+[0-9]+$"))
        {
            validationMessage = "Agent must start with uppercase letters, end with numbers, and contain no spaces or special characters.";
        }
        else if (editAgentValues.Contains(newAgent))
        {
            validationMessage = "Agent must be unique.";
        }
        else
        {
            editAgentValues.Add(newAgent.Trim());
            newAgent = string.Empty;
            validationMessage = string.Empty;
        }
        StateHasChanged();
    }

    public void RemoveAgent(int index)
    {
        if (index >= 0 && index < editAgentValues.Count)
        {
            editAgentValues.RemoveAt(index);
            if (editIndex == index)
            {
                editIndex = null;
            }
        }
        StateHasChanged();
    }

    public void SaveAllChanges()
    {
        // Ensure no duplicates or invalid values exist in the list
        if (editAgentValues.Distinct().Count() != editAgentValues.Count)
        {
            validationMessage = "All agent values must be unique.";
            return;
        }

        // Apply all changes to the main Agents list
        Agents = new List<string>(editAgentValues);
        validationMessage = string.Empty;
        editIndex = null;
        StateHasChanged();
    }
}
