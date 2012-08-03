---
comments: true
date: 2010-11-01 17:22:43
layout: post
slug: silverlight-single-click-checkbox-datagrid-columns
title: Silverlight single-click checkbox DataGrid columns
wordpress_id: 240
---

The title for this post is a mouthful so let me describe the back story a little bit. In Microsoft Silverlight applications we often use DataGrid controls that are databound with a boolean data field that is represented by a check box. Silverlight provides a built-in column type of DataGridCheckBoxColumn, which will display the underlying boolean data field as a standard CheckBox control inline in the DataGrid cell.

Setting up a DataGrid column using a DataGridCheckBoxColumn looks like this:

``` xml

<sdk:DataGrid.Columns>
              <sdk:DataGridTextColumn Header="Name" Binding="{Binding Name}" />
             <sdk:DataGridCheckBoxColumn Header="Select" Binding="{Binding Selected}" />
</sdk:DataGrid.Columns>

```


However the default behavior of a databound cell in a DataGrid is to require a click on the cell itself before we can edit its contents. In the case of text fields this makes a lot of sense. You don't want to enter edit mode immediately upon selecting a row in the grid. However, in the case of a check box, it is often the case that we'll want to quickly check off several items in the list. Having to select the cell first means that each selection action requires two clicks in order to change the state of the check box.

The first click on the check box leaves the DataGrid looking like this:
[![](http://newcome.files.wordpress.com/2010/11/checkbox1.png)](http://newcome.files.wordpress.com/2010/11/checkbox1.png)

There doesn't seem to be much out there on forums or blogs about this issue save for [this forum posting](http://forums.silverlight.net/forums/p/35476/106883.aspx). One of the commenters has it almost right and a second commenter corrects him, but the resulting code is never provided. I had to fix this in my own application, so here is what I came up with:

``` xml

           <sdk:DataGridTemplateColumn>
                        <sdk:DataGridTemplateColumn.CellEditingTemplate>
                            <DataTemplate>
                                <CheckBox IsThreeState="False" IsChecked="{Binding Path=Selected, Mode=TwoWay}" HorizontalAlignment="Center" VerticalAlignment="Center" />
                            </DataTemplate>
                        </sdk:DataGridTemplateColumn.CellEditingTemplate>
                    </sdk:DataGridTemplateColumn>

```


The solution to this problem was out there, but none of the sources described the actual problem well enough to be found easily by searching. Hopefully this post rectifies that situation.

