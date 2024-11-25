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
                @foreach (var agent in Agents.Select((value, index) => new { value, index }))
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
        <SfButton OnClick="AddAgent" Content="Save changes"></SfButton>
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
        Agents.Add("BLUEDK29");
        Agents.Add("BLUEFI29");
        Agents.Add("BLUENO29");
        Agents.Add("BLUESE29");

        editAgentValues = new List<string>(Agents); 
    }

    public void EnableEditing(int index)
    {
        editIndex = index;
    }

    public void SaveAgent(int index)
    {
        if (index >= 0 && index < Agents.Count)
        {
            Agents[index] = editAgentValues[index];
        }
        editIndex = null; 
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
        else if (Agents.Contains(newAgent))
        {
            validationMessage = "Agent must be unique.";
        }
        else
        {
            Agents.Add(newAgent);
            editAgentValues.Add(newAgent); 
            newAgent = string.Empty;
            validationMessage = string.Empty; 
            StateHasChanged();
        }
    }

    public void RemoveAgent(int index)
    {
        if (index >= 0 && index < Agents.Count)
        {
            Agents.RemoveAt(index);
            editAgentValues.RemoveAt(index);
            StateHasChanged();
        }
    }
}

<style>
    .table {
        width: 100%;
        font-size: 13px;
    }

        .table th, .table td {
            padding: 8px;
        }

    .action-section {
        text-align: right;
    }

    .settings-section {
        margin: 1rem;
        border: 2px solid #ccc;
        border-radius: 5px;
        box-shadow: 3px 3px 5px rgba(0, 0, 0, 0.2);
        width: 40%;
        padding: 0;
    }

    .settings-card-title {
        display: flex;
        justify-content: space-between;
        align-items: center;
        background-color: #F7F7F7;
        padding: 10px 15px;
        font-weight: bold;
        font-size: 13px;
        border-radius: 8px 8px 0 0;
        border-bottom: 1px solid #ccc;
    }

    SfButton {
        margin-left: auto;
    }

    .settings-card-content {
        padding: 10px 15px;
        font-size: 13px;
        background-color: #ffffff;
        border-bottom-right-radius: 5px;
        border-bottom-left-radius: 5px;
    }

    .settings-buttons {
        display: flex;
        justify-content: flex-end;
        padding: 10px 15px;
    }

    .settings-cancel-button {
        background-color: transparent;
        border: none !important;
        color: #6c757d;
        font-weight: bold;
        margin-right: 0.5rem;
    }

    .settings-save-button {
        background-color: #ff9900;
        color: #000;
        font-weight: bold;
    }

    .settings-buttons .settings-save-button:hover {
        background-color: #ff9900;
        color: #000;
        opacity: 0.8 !important;
    }

    .settings-buttons .settings-cancel-button:hover {
        background-color: transparent;
        color: #6c757d;
        opacity: 0.8 !important;
    }
</style>
