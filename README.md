<SfDialog Width="600px" Header="Transaction Message" IsModal="true">
    <div class="formatted-message">
        @((MarkupString)FormattedMessage)
    </div>
    <SfButton Content="Close" OnClick="CloseMessageDialog" />
</SfDialog>

@code {
    private string FormattedMessage = string.Empty;

    private async Task OpenMessageDialog()
    {
        var rawMessage = await TransactionService.GetTransactionMessage(Wiretransfer.Guid);
        FormattedMessage = FormatMessage(rawMessage);
        MessageDialog.ShowAsync();
    }

    private void CloseMessageDialog()
    {
        MessageDialog.HideAsync();
    }

    private string FormatMessage(string rawMessage)
    {
        var htmlBuilder = new System.Text.StringBuilder();
        htmlBuilder.Append("<div style='font-family: Arial; font-size: 14px; line-height: 1.6;'>");

        // Use regex to extract blocks like {1:...}, 2, 3
        var regex = new System.Text.RegularExpressions.Regex(@"\{(\d+):(.*?)\}");
        var matches = regex.Matches(rawMessage);

        foreach (System.Text.RegularExpressions.Match match in matches)
        {
            var sectionNumber = match.Groups[1].Value;
            var content = match.Groups[2].Value;

            // Display section header
            htmlBuilder.AppendFormat("<div><strong>Section {0}:</strong></div>", sectionNumber);

            // Process content within the section
            var lines = content.Split(new[] { '\n' }, StringSplitOptions.RemoveEmptyEntries);
            foreach (var line in lines)
            {
                if (line.StartsWith(":"))
                {
                    // Handle lines starting with colon codes
                    var code = line.Substring(1, 3);
                    var value = line.Substring(4);
                    htmlBuilder.AppendFormat("<div style='margin-left: 20px;'><strong>{0}:</strong> {1}</div>", code, value);
                }
                else
                {
                    // Handle other lines
                    htmlBuilder.AppendFormat("<div style='margin-left: 40px;'>{0}</div>", line);
                }
            }
        }

        htmlBuilder.Append("</div>");
        return htmlBuilder.ToString();
    }
}
